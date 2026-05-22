# 🔍 ფაილებისა და ტექსტის ძიება

## სარჩევი
- [`grep` — ტექსტში ძიება](#-grep--ტექსტში-ძიება)
- [`find` — ფაილების ძიება](#-find--ფაილების-ძიება-ფაილურ-სისტემაში)
- [`which` — ბრძანების მდებარეობა](#-which--ბრძანების-მდებარეობა)
- [`whereis` — ბრძანება + man + source](#-whereis--ბრძანება--man--source)
- [`locate` — სწრაფი ფაილური ძიება](#-locate--სწრაფი-ფაილური-ძიება)
- [სავარჯიშოები](#-სავარჯიშოები)

---

### 🔎 `grep` – ტექსტში ძიება

**მოკლე აღწერა:** ეძებს ტექსტს ფაილში ან stdin-დან მიღებულ შემოდინებაში.

```bash
grep "error" logfile.txt          # ეძებს სიტყვა "error"-ს ფაილში
grep -i "hello" file.txt          # იგნორირებს პატარა/დიდ ასოებს (case-insensitive)
grep -r "TODO" ./project          # რეკურსიული ძიება დირექტორიაში
grep -n "main" *.c                # აჩვენებს ხაზის ნომერსაც
grep -v "DEBUG" log.txt           # ინვერსია — ხაზები რომლებიც *არ* შეიცავენ "DEBUG"-ს
grep -c "ERROR" app.log           # მხოლოდ შესაბამისობების რაოდენობა
grep -l "TODO" *.md               # მხოლოდ ფაილების სია, არა შინაარსი
grep -E "error|warn|fail" log     # extended regex (ალტერნატივა: egrep)
grep -A 3 "Exception" app.log     # ნაპოვნი ხაზის შემდეგ 3 ხაზი
grep -B 2 "Exception" app.log     # ნაპოვნი ხაზის წინა 2 ხაზი
grep -C 1 "Exception" app.log     # ნაპოვნი ხაზის გარშემო 1 ხაზი (context)
ps aux | grep nginx               # pipe-ის გავლით ფილტრაცია
```

> 💡 უფრო სწრაფი ალტერნატივები: [`ripgrep`](https://github.com/BurntSushi/ripgrep) (`rg`), [`ag`](https://github.com/ggreer/the_silver_searcher).

---

### 🧭 `find` – ფაილების ძიება ფაილურ სისტემაში

**მოკლე აღწერა:** პოულობს ფაილებს დირექტორიებში სხვადასხვა კრიტერიუმით (სახელი, ზომა, ტიპი, თარიღი, უფლებები).

```bash
find . -name "*.log"              # ყველა .log ფაილი მიმდინარე დირექტორიიდან
find . -iname "*.JPG"             # case-insensitive
find . -type f                    # მხოლოდ ფაილები
find . -type d                    # მხოლოდ დირექტორიები
find . -type l                    # მხოლოდ symlink-ები

find /etc -size +1M               # 1MB-ზე დიდი ფაილები
find . -size -100c                # 100 ბაიტზე ნაკლები
find . -empty                     # ცარიელი ფაილები/დირექტორიები

find . -mtime -1                  # ბოლო 1 დღეში შეცვლილი
find . -mtime +30                 # 30 დღეზე ძველი
find . -mmin -10                  # ბოლო 10 წუთში შეცვლილი

find . -perm 644                  # ფაილები 644 უფლებებით
find / -perm -4000 -type f        # ყველა setuid ფაილი
find . -user david                # მფლობელის მიხედვით

# მოქმედებები ნაპოვნ ფაილებზე:
find . -name "*.tmp" -delete                    # წაშლა
find . -name "*.log" -exec gzip {} \;           # შეკუმშვა
find . -name "*.bak" -exec rm -i {} \;          # ინტერაქტიული წაშლა
find . -name "*.txt" -exec grep "TODO" {} +     # grep ყველაში
```

---

### 📍 `which` – ბრძანების მდებარეობა

**მოკლე აღწერა:** აჩვენებს, რომელი ფაილი გაეშვება როცა შენ ბრძანებას ჩაწერ. ეძებს `$PATH`-ში.

```bash
which python              # /usr/bin/python
which -a python           # ყველა ნაპოვნი (თუ რამდენიმე ვერსიაა)
which ls cd               # რამდენიმე ერთდროულად
```

> ⚠️ `which` ვერ პოულობს shell-ის ბილტინებს (`cd`, `echo`) — ამისთვის გამოიყენე `type`.

---

### 📚 `whereis` – ბრძანება + man + source

**მოკლე აღწერა:** პოულობს ბრძანების **ბინარული ფაილის**, **source code**-ისა და **man-გვერდის** მდებარეობას.

```bash
whereis ls
# ls: /usr/bin/ls /usr/share/man/man1/ls.1.gz

whereis -b nginx          # მხოლოდ ბინარული
whereis -m nginx          # მხოლოდ man
```

---

### ⚡ `locate` – სწრაფი ფაილური ძიება

**მოკლე აღწერა:** ეძებს ფაილს წინასწარ აშენებულ მონაცემთა ბაზაში — `find`-ზე ბევრად სწრაფი, მაგრამ შეიძლება მონაცემები მოძველებული იყოს.

```bash
locate sshd_config        # სწრაფი ძიება
locate -i README          # case-insensitive
locate -c "*.conf"        # მხოლოდ რაოდენობა
sudo updatedb             # ბაზის ხელით განახლება
```

> 💡 თუ `locate` არ არის დაყენებული: `sudo apt install plocate` (Debian/Ubuntu) ან `sudo dnf install mlocate` (Fedora).

---

## 🎯 სავარჯიშოები

1. იპოვე ყველა `.md` ფაილი, რომელშიც წერია სიტყვა "TODO".
2. იპოვე home დირექტორიაში 100MB-ზე დიდი ფაილები.
3. იპოვე და წაშალე ყველა `.bak` ფაილი მიმდინარე დირექტორიის ქვეშ (ჯერ -delete-ის გარეშე გადაამოწმე).
4. ისარგებლე `grep -C 2`-ით თქვენი ერთ-ერთი ლოგ ფაილიდან ერთი error-ის კონტექსტის სანახავად.
5. შეადარე `which`, `whereis` და `type` ბრძანებების შედეგი `ls` ბრძანებაზე.
