# 📦 არქივები და კომპრესია

Linux-ში არსებობს **ორი ცნება**, რომელიც ხშირად ერევა ერთმანეთში:
- **არქივი** — რამდენიმე ფაილის ერთში გაერთიანება (`tar`).
- **კომპრესია** — ფაილის ზომის შემცირება (`gzip`, `bzip2`, `xz`).

ჩვეულებრივ ორივე ერთად გამოიყენება: `tar` ფაილებს აერთიანებს, შემდეგ კომპრესორი (`gzip` და ა.შ.) ცდის ზომას.

## სარჩევი
- [`tar` — არქივის შექმნა და გაშლა](#-tar--არქივის-შექმნა-და-გაშლა)
- [`gzip` / `gunzip`](#-gzip--gunzip)
- [`bzip2` და `xz`](#-bzip2-და-xz)
- [`zip` / `unzip`](#-zip--unzip)
- [შედარების ცხრილი](#-შედარების-ცხრილი)
- [სავარჯიშოები](#-სავარჯიშოები)

---

### 📦 `tar` – არქივის შექმნა და გაშლა

დროშები: **c** create, **x** extract, **t** list, **v** verbose, **f** file, **z** gzip, **j** bzip2, **J** xz.

```bash
# შექმნა (.tar.gz / .tgz):
tar -czvf backup.tar.gz folder/
tar -czvf logs.tar.gz /var/log/*.log

# გაშლა:
tar -xzvf backup.tar.gz                   # მიმდინარე დირექტორიაში
tar -xzvf backup.tar.gz -C /tmp/restore   # კონკრეტულ ადგილას

# ნახვა გაშლის გარეშე:
tar -tzvf backup.tar.gz

# კონკრეტული ფაილის გაშლა:
tar -xzvf backup.tar.gz folder/specific.txt

# bzip2-ით (უკეთესი კომპრესია, ნელი):
tar -cjvf backup.tar.bz2 folder/

# xz-ით (კიდევ უკეთესი, კიდევ ნელი):
tar -cJvf backup.tar.xz folder/

# გამორიცხვა:
tar -czvf backup.tar.gz folder/ --exclude='*.log' --exclude='node_modules'
```

> 💡 თანამედროვე `tar` ავტომატურად ცნობს კომპრესიის ფორმატს გაშლისას: `tar -xvf file.tar.gz` ისეც იმუშავებს. `z`/`j`/`J`-ის გარეშე.

---

### 🗜️ `gzip` / `gunzip`

```bash
gzip file.txt                    # ქმნის file.txt.gz, ორიგინალს შლის
gzip -k file.txt                 # -k = keep ორიგინალი
gunzip file.txt.gz               # უკან გაშლა (== gzip -d)
gzip -9 file.txt                 # მაქსიმალური კომპრესია (1=fast, 9=best)

zcat file.txt.gz                 # gzip ფაილის წაკითხვა გაშლის გარეშე
zless app.log.gz                 # less-ის ეკვივალენტი
zgrep "error" app.log.gz         # grep არქივში
```

---

### 🗜️ `bzip2` და `xz`

```bash
bzip2 file.txt                   # file.txt.bz2
bunzip2 file.txt.bz2

xz file.txt                      # file.txt.xz
unxz file.txt.xz
xzcat file.txt.xz                # წაკითხვა გაშლის გარეშე
```

---

### 🗂️ `zip` / `unzip`

ეს ფორმატი Linux-ში ნაკლებად popular-ია, მაგრამ საჭიროა Windows-თან თავსებადობისთვის.

```bash
zip archive.zip file1 file2 file3
zip -r archive.zip folder/                 # რეკურსიული
zip -e secret.zip file.txt                 # პაროლით დაცული

unzip archive.zip
unzip archive.zip -d /tmp/destination
unzip -l archive.zip                       # შინაარსის სია
```

---

### 📊 შედარების ცხრილი

| ფორმატი | სიჩქარე | კომპრესია | CPU |
|---------|---------|-----------|-----|
| `gzip` (.gz) | სწრაფი | საშუალო | დაბალი |
| `bzip2` (.bz2) | ნელი | კარგი | საშუალო |
| `xz` (.xz) | ძალიან ნელი | საუკეთესო | მაღალი |
| `zip` | სწრაფი | საშუალო | დაბალი (cross-platform) |
| `zstd` (.zst) | ძალიან სწრაფი | ძალიან კარგი | დაბალი (თანამედროვე) |

> 💡 თანამედროვე ალტერნატივა: `zstd` — gzip-ის სიჩქარით, xz-ის ახლოს კომპრესიით.

---

## 🎯 სავარჯიშოები

1. შექმენი `/etc`-ის სარეზერვო ასლი `tar.gz` ფორმატით თქვენი home-ში.
2. შეადარე `gzip`, `bzip2`, `xz`-ის ფაილის ზომის შემცირება ერთ და იმავე დიდი ფაილზე.
3. ნახე .tar.gz არქივის შინაარსი მისი გაშლის გარეშე.
4. შექმენი არქივი ისე, რომ გამორიცხო ყველა `.log` და `node_modules/` დირექტორია.
5. ისარგებლე `zgrep`-ით — იპოვე "ERROR" სიტყვა გრამზრდილ log ფაილში გაშლის გარეშე.
