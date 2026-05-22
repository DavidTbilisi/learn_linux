# 🔐 SSH და დისტანციური წვდომა

SSH (Secure Shell) — დაშიფრული პროტოკოლი დისტანციური სერვერებთან მუშაობისთვის. ფაქტობრივი სტანდარტი Linux ადმინისტრირებაში.

## სარჩევი
- [`ssh` — დაკავშირება](#-ssh--დაკავშირება)
- [`ssh-keygen` — გასაღების შექმნა](#-ssh-keygen--გასაღების-შექმნა)
- [`ssh-copy-id` — public key-ის გადატანა](#-ssh-copy-id--public-key-ის-გადატანა)
- [`~/.ssh/config` — alias-ები](#-sshconfig--alias-ები)
- [`scp` — ფაილების კოპირება](#-scp--ფაილების-კოპირება)
- [`rsync` — სინქრონიზაცია](#-rsync--სინქრონიზაცია)
- [SSH tunneling](#-ssh-tunneling)
- [სავარჯიშოები](#-სავარჯიშოები)

---

### 🔌 `ssh` – დაკავშირება

```bash
ssh user@host                    # ძირითადი დაკავშირება
ssh user@host -p 2222            # სხვა პორტი
ssh user@host "ls -la /var/log"  # ერთჯერადი ბრძანების გაშვება
ssh -v user@host                 # verbose (debug)
ssh -i ~/.ssh/special_key user@host    # კონკრეტული გასაღები
```

პირველ შეხვედრაზე გადაგეცემა server-ის fingerprint — დაეთანხმე (`yes`), რომ ჩაიწეროს `~/.ssh/known_hosts`-ში.

---

### 🔑 `ssh-keygen` – გასაღების შექმნა

გასაღების წყვილი: **private** (`~/.ssh/id_ed25519`, **არასოდეს** არ გასცე) + **public** (`~/.ssh/id_ed25519.pub`, შეგიძლია გასცე).

```bash
ssh-keygen -t ed25519 -C "your_email@example.com"     # თანამედროვე, რეკომენდებული
ssh-keygen -t rsa -b 4096 -C "you@example.com"        # ძველი ალტერნატივა

# პროცესში გკითხავს:
#   - სად შეინახოს (default: ~/.ssh/id_ed25519)
#   - passphrase (რეკომენდებულია — დაიცავს გასაღებს, თუ მოიპარეს)

ssh-keygen -l -f ~/.ssh/id_ed25519.pub                # fingerprint-ი
ssh-keygen -p -f ~/.ssh/id_ed25519                    # passphrase-ის შეცვლა
```

`ssh-agent` ინახავს passphrase-ს session-ის განმავლობაში:
```bash
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_ed25519
```

---

### 📤 `ssh-copy-id` – public key-ის გადატანა

ავტომატურად დააკოპირებს შენი public key-ს server-ის `~/.ssh/authorized_keys`-ში.

```bash
ssh-copy-id user@host                            # ნაგულისხმევი გასაღები
ssh-copy-id -i ~/.ssh/special_key.pub user@host  # კონკრეტული
ssh-copy-id -p 2222 user@host
```

ამის შემდეგ შეგიძლია შეხვიდე **პაროლის გარეშე**.

ხელით ალტერნატივა:
```bash
cat ~/.ssh/id_ed25519.pub | ssh user@host "mkdir -p ~/.ssh && cat >> ~/.ssh/authorized_keys"
```

---

### ⚙️ `~/.ssh/config` – alias-ები

ხშირი sserver-ებისთვის შექმენი მოკლე სახელები:

```
# ~/.ssh/config
Host prod
    HostName 192.168.1.100
    User deploy
    Port 2222
    IdentityFile ~/.ssh/prod_key

Host github.com
    User git
    IdentityFile ~/.ssh/github_key
    AddKeysToAgent yes

Host *
    ServerAliveInterval 60
    ServerAliveCountMax 3
```

ახლა მუშაობს უბრალოდ:
```bash
ssh prod
scp file.txt prod:/tmp/
```

---

### 📁 `scp` – ფაილების კოპირება

```bash
scp file.txt user@host:/tmp/                # ლოკალურიდან → დისტანციური
scp user@host:/etc/nginx.conf ./            # დისტანციური → ლოკალური
scp -r folder/ user@host:/opt/              # რეკურსიული
scp -P 2222 file.txt user@host:/tmp/        # სხვა პორტი (P დიდი!)
scp host1:/path/file host2:/path/           # სერვერებს შორის (კარგი ქსელით)
```

> 💡 თანამედროვე ალტერნატივა: `rsync` — უფრო სწრაფი (delta-transfer) და მოქნილი.

---

### 🔄 `rsync` – სინქრონიზაცია

გადააქვს მხოლოდ ცვლილებები (delta) — დიდი დირექტორიების სინქრონიზაციისთვის შეუცვლელია.

```bash
rsync -avz folder/ user@host:/backup/folder/     # სტანდარტული backup
rsync -avz --progress big.iso user@host:/tmp/    # პროგრესის ჩვენებით
rsync -avz --delete src/ dest/                   # ჩამოშორდი დანამატ ფაილებს dest-ში
rsync -avz --dry-run src/ dest/                  # ჯერ ნახე რა მოხდება

# დროშები:
#   -a archive (= -rlptgoD: rec + symlink + perms + time + group + owner + devices)
#   -v verbose
#   -z კომპრესია გადაცემისას
#   -P = --progress --partial
#   -e "ssh -p 2222" — სხვა პორტი
```

> ⚠️ **trailing slash-ი მნიშვნელოვანია**:
> - `rsync src/ dst/` → დააკოპირებს `src`-ის **შინაარსს** `dst`-ში
> - `rsync src dst/` → დააკოპირებს მთლიან `src` დირექტორიას `dst`-ის შიგნით

---

### 🚇 SSH tunneling

**Local forwarding (-L)** — ლოკალური პორტი → დისტანციური სერვისი:
```bash
ssh -L 8080:localhost:80 user@host
# ახლა http://localhost:8080 = http://host:80
```

**Remote forwarding (-R)** — დისტანციური პორტი → ლოკალური სერვისი:
```bash
ssh -R 9000:localhost:3000 user@host
# host-ის 9000 → შენი ლოკალური 3000-ი
```

**SOCKS proxy (-D)**:
```bash
ssh -D 1080 user@host
# ბრაუზერი → SOCKS5 localhost:1080 → traffic გადის host-ის გავლით
```

---

## 🎯 სავარჯიშოები

1. შექმენი ed25519 SSH key, დააკოპირე public-ი GitHub-ში — push შეგეძლოს HTTPS-ის ნაცვლად SSH-ით.
2. დაამატე `~/.ssh/config`-ში alias შენი სასურველი server-ისთვის და სცადე `ssh ALIAS`.
3. `rsync`-ით სინქრონიზე ლოკალური დირექტორია დისტანციურ server-ზე — გადაამოწმე -z და -P დროშები.
4. `scp` და `rsync`-ის სიჩქარე შეადარე ერთი და იმავე დიდი ფაილზე (`time` ბრძანებით).
5. გახსენი ლოკალური SSH tunnel server-ის MySQL-ისკენ (პორტი 3306) და დაუკავშირდი ლოკალური კლიენტით.
