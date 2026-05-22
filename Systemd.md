# 🖥️ systemd და სერვისები

`systemd` — Linux-ის init სისტემა და სერვისების მენეჯერი (ცვლის ძველ SysV init-ს). მისი ხელსაწყოა `systemctl` (სერვისების მართვა) და `journalctl` (ლოგების კითხვა).

## სარჩევი
- [`systemctl` — სერვისების მართვა](#-systemctl--სერვისების-მართვა)
- [სერვისების ჩამოთვლა](#-სერვისების-ჩამოთვლა)
- [საკუთარი `.service` ფაილი](#-საკუთარი-service-ფაილი)
- [`journalctl` — ლოგების კითხვა](#-journalctl--ლოგების-კითხვა)
- [`systemd` timer-ები (cron-ის ალტერნატივა)](#-systemd-timer-ები)
- [Targets / runlevel-ები](#-targets--runlevel-ები)
- [სავარჯიშოები](#-სავარჯიშოები)

---

### ⚙️ `systemctl` – სერვისების მართვა

```bash
sudo systemctl start nginx           # გაშვება
sudo systemctl stop nginx            # გაჩერება
sudo systemctl restart nginx         # გადატვირთვა
sudo systemctl reload nginx          # config-ის reload (გაჩერების გარეშე)
sudo systemctl status nginx          # სტატუსი + ბოლო ლოგი

sudo systemctl enable nginx          # ჩატვირთვაზე ავტო-გაშვება
sudo systemctl disable nginx         # ავტო-გაშვების გათიშვა
sudo systemctl enable --now nginx    # enable + start ერთად

systemctl is-active nginx            # active / inactive / failed
systemctl is-enabled nginx
systemctl is-failed nginx
```

---

### 📋 სერვისების ჩამოთვლა

```bash
systemctl list-units --type=service              # აქტიური სერვისები
systemctl list-units --type=service --all        # ყველა (აქტიური + ჩატვირთული მაგრამ stopped)
systemctl list-unit-files --type=service         # ყველა .service ფაილი + enabled/disabled
systemctl --failed                               # მხოლოდ ჩავარდნილი
```

---

### 📝 საკუთარი `.service` ფაილი

დასაწერი გზა: `/etc/systemd/system/myapp.service`

```ini
[Unit]
Description=My Application
After=network.target

[Service]
Type=simple
User=david
WorkingDirectory=/opt/myapp
ExecStart=/usr/bin/python3 /opt/myapp/server.py
Restart=on-failure
RestartSec=5
StandardOutput=journal
StandardError=journal

[Install]
WantedBy=multi-user.target
```

შემდეგ:

```bash
sudo systemctl daemon-reload         # systemd-ს უთხარი, რომ ფაილი წაიკითხოს
sudo systemctl enable --now myapp    # ჩაატვირთე + ავტო-გაშვება
systemctl status myapp
```

ხშირი `Type` მნიშვნელობები: `simple` (foreground process), `forking` (კლასიკური daemon), `oneshot` (ერთჯერადი ამოცანა), `notify` (systemd-ს ეუბნება ready-ს).

---

### 📜 `journalctl` – ლოგების კითხვა

`systemd` ლოგებს ცენტრალურად აგროვებს. `journalctl` მისი წამკითხავია.

```bash
journalctl                       # ყველაფერი (q-ით გასვლა)
journalctl -e                    # ბოლოში გადასვლა
journalctl -f                    # ცოცხალი თვალთვალი (tail -f-ის ეკვივალენტი)
journalctl -n 50                 # ბოლო 50 ხაზი

journalctl -u nginx              # კონკრეტული სერვისი
journalctl -u nginx -f           # სერვისის ცოცხალი ლოგი
journalctl -u nginx --since "1 hour ago"
journalctl -u nginx --since "2026-05-22 10:00" --until "2026-05-22 12:00"

journalctl -p err                # მხოლოდ error და უარესი
journalctl -k                    # მხოლოდ kernel-ის შეტყობინებები (dmesg-ის ეკვივალენტი)
journalctl -b                    # მიმდინარე boot-ის ლოგი
journalctl -b -1                 # წინა boot
journalctl --list-boots
```

ლოგების მოცულობის შემოწმება:
```bash
journalctl --disk-usage
sudo journalctl --vacuum-time=2weeks    # 2 კვირაზე ძველის წაშლა
sudo journalctl --vacuum-size=500M
```

---

### ⏰ systemd timer-ები

`cron`-ის თანამედროვე ალტერნატივა, რომელიც ლოგებიც აქვს და უკეთესი monitoring-ი.

`/etc/systemd/system/backup.service`:
```ini
[Unit]
Description=Daily Backup
[Service]
Type=oneshot
ExecStart=/usr/local/bin/backup.sh
```

`/etc/systemd/system/backup.timer`:
```ini
[Unit]
Description=Daily Backup Timer
[Timer]
OnCalendar=daily
Persistent=true
[Install]
WantedBy=timers.target
```

```bash
sudo systemctl enable --now backup.timer
systemctl list-timers                  # ყველა აქტიური timer
```

---

### 🎯 Targets / runlevel-ები

`systemd` target — ძველი runlevel-ის ანალოგი (სისტემის "რეჟიმი").

| Target | აღწერა |
|--------|--------|
| `poweroff.target` | გათიშვა |
| `rescue.target` | single-user mode |
| `multi-user.target` | სრული მრავალმომხმარებლიანი (server-ის ნაგულისხმევი) |
| `graphical.target` | + GUI (desktop-ის ნაგულისხმევი) |
| `reboot.target` | გადატვირთვა |

```bash
systemctl get-default
sudo systemctl set-default multi-user.target
sudo systemctl isolate rescue.target     # შესვლა რეჟიმში "ახლავე"
```

---

## 🎯 სავარჯიშოები

1. შექმენი მინიმალური `.service` ფაილი, რომელიც გაუშვებს მარტივ Python HTTP სერვერს, და ჩართე ის.
2. ნახე, რომელი სერვისები ჩაარდა ბოლო boot-ში: `systemctl --failed` + `journalctl -p err -b`.
3. დაწერე `.timer` ფაილი, რომელიც ყოველ 10 წუთში ერთხელ გადაუშვებს მარტივ "echo" სკრიპტს, ნახე ლოგი.
4. გადაამოწმე journal-ის მოცულობა და გაასუფთავე 1 კვირაზე ძველი ჩანაწერები.
