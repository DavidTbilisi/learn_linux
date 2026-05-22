# 🧑‍🔧 მომხმარებლები და ჯგუფები

Linux-ი მრავალმომხმარებლიანი სისტემაა. ფაილებზე წვდომა, პროცესების გაშვება, `sudo`-ს უფლება — ყველაფერი ბმულია იუზერთან და მის ჯგუფებთან.

## სარჩევი
- [მიმდინარე იუზერი: `whoami`, `id`, `groups`](#-მიმდინარე-იუზერი)
- [იუზერების მართვა: `useradd`, `usermod`, `userdel`](#-იუზერების-მართვა)
- [`passwd` — პაროლი](#-passwd--პაროლი)
- [ჯგუფების მართვა: `groupadd`, `groupdel`, `gpasswd`](#-ჯგუფების-მართვა)
- [`su`, `sudo`, `visudo`](#-su-sudo-visudo)
- [ფაილების სტრუქტურა: `/etc/passwd`, `/etc/shadow`, `/etc/group`](#-სისტემური-ფაილები)
- [სავარჯიშოები](#-სავარჯიშოები)

---

### 🪪 მიმდინარე იუზერი

```bash
whoami                  # ჩემი username
id                      # uid, gid, ჯგუფები
id david                # სხვა იუზერის შესახებ
groups                  # ჩემი ჯგუფები
groups david            # ვინმე სხვის
who                     # ვინ არის ახლა შესული
w                       # who + რას აკეთებენ
last                    # ბოლო ლოგინების ისტორია
```

---

### 👤 იუზერების მართვა

ყველა ეს ბრძანება საჭიროებს `sudo`-ს.

```bash
sudo useradd alice                   # მინიმალური იუზერი (Debian-ზე home არ შექმნება!)
sudo useradd -m -s /bin/bash alice   # home + shell
sudo useradd -m -G sudo,docker alice # დამატებითი ჯგუფებით

sudo usermod -aG sudo alice          # არსებულ იუზერს დაამატე ჯგუფი (-a აუცილებელია!)
sudo usermod -l newname alice        # username-ის შეცვლა
sudo usermod -L alice                # ანგარიშის გაყინვა (lock)
sudo usermod -U alice                # ანგარიშის გათავისუფლება

sudo userdel alice                   # წაშლა (home რჩება)
sudo userdel -r alice                # წაშლა + home + mail spool
```

> ⚠️ `usermod -G group user`-ი **ანაცვლებს** ჯგუფების სიას. **ყოველთვის გამოიყენე `-aG`**, თუ უბრალოდ ემატება!

> 💡 Debian/Ubuntu-ში არსებობს უფრო მოსახერხებელი `adduser` (interactive) — `useradd`-ის wrapper-ი.

---

### 🔑 `passwd` – პაროლი

```bash
passwd                  # ჩემი პაროლის შეცვლა
sudo passwd alice       # სხვის პაროლის გადატვირთვა (admin)
sudo passwd -l alice    # პაროლის გათიშვა
sudo passwd -e alice    # მოითხოვება ცვლილება შემდეგ ლოგინზე
```

---

### 👥 ჯგუფების მართვა

```bash
sudo groupadd developers
sudo groupdel developers
sudo gpasswd -a alice developers     # იუზერის დამატება ჯგუფში
sudo gpasswd -d alice developers     # იუზერის წაშლა ჯგუფიდან

# ალტერნატივა:
sudo usermod -aG developers alice
```

> 💡 ჯგუფის ცვლილება მოქმედებს მხოლოდ **მომდევნო ლოგინიდან**. სასწრაფოდ რომ ამოქმედდეს — `newgrp developers` ან გათიშე/შემოდი.

---

### 🛡️ `su`, `sudo`, `visudo`

```bash
su -                    # სრული root shell (root-ის პაროლი საჭიროა)
su - alice              # სხვა იუზერის shell
sudo command            # ერთჯერადი root-ით გაშვება
sudo -i                 # interactive root shell
sudo -u alice command   # სხვა იუზერის სახელით

sudo visudo             # `/etc/sudoers`-ის უსაფრთხო რედაქტირება
```

`/etc/sudoers`-ის სტრიქონის მაგალითები:
```
alice ALL=(ALL:ALL) ALL                    # სრული sudo უფლება
%developers ALL=(ALL) NOPASSWD: /bin/systemctl restart nginx
```

> ⚠️ არასოდეს გახსნა `/etc/sudoers` ჩვეულებრივი რედაქტორით! `visudo` სინტაქსს ამოწმებს — შეცდომა შეიძლება სისტემიდან გაგრიყოს.

---

### 📂 სისტემური ფაილები

* **`/etc/passwd`** — იუზერების ბაზა (იკითხება ყველასთვის):
  ```
  alice:x:1001:1001:Alice Smith:/home/alice:/bin/bash
  username:passwd_placeholder:UID:GID:GECOS:home:shell
  ```

* **`/etc/shadow`** — დაშიფრული პაროლები (იკითხება მხოლოდ root-ისთვის):
  ```
  alice:$6$hash...:19500:0:99999:7:::
  ```

* **`/etc/group`** — ჯგუფების ბაზა:
  ```
  developers:x:1010:alice,bob,carol
  ```

---

## 🎯 სავარჯიშოები

1. შექმენი იუზერი `test_user` home-ით და bash-ით, შემდეგ შედი მისი სახელით (`su -`).
2. დაამატე `test_user` `sudo` ჯგუფში და გადაამოწმე — `groups test_user`.
3. `last`-ის გავლით ნახე ვინ შემოვიდა შენს მანქანაში ბოლო 5 ჯერ.
4. `/etc/passwd`-დან გაარკვიე, რომელ იუზერებს აქვთ ნამდვილი shell (არა `/usr/sbin/nologin` ან `/bin/false`).
5. წაშალე `test_user` მისი home-ის ჩათვლით.
