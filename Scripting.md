# 🐚 Bash სკრიპტინგი — საფუძვლები

## სარჩევი
- [Shebang და გაშვება](#-shebang-და-გაშვება)
- [ცვლადები და environment](#-ცვლადები-და-environment)
- [არგუმენტები](#-არგუმენტები)
- [if / elif / else](#-if--elif--else)
- [ციკლები: for, while, until](#-ციკლები-for-while-until)
- [ფუნქციები](#-ფუნქციები)
- [Exit codes](#-exit-codes)
- [Redirection და pipes](#-redirection-და-pipes)
- [Command substitution](#-command-substitution)
- [Best practices: `set -euo pipefail`](#-best-practices-set--euo-pipefail)
- [სავარჯიშოები](#-სავარჯიშოები)

---

### 🪧 Shebang და გაშვება

`#!/path/to/interpreter` — სკრიპტის პირველი სტრიქონი ეუბნება სისტემას, რომელი ინტერპრეტატორი გაუშვას.

```bash
#!/bin/bash
echo "გამარჯობა, $USER!"
```

```bash
chmod +x hello.sh    # მიეცი გაშვების უფლება
./hello.sh           # გაუშვი
bash hello.sh        # ალტერნატივა — shebang-ის გარეშეც მუშაობს
```

---

### 📦 ცვლადები და environment

```bash
name="David"             # მინიჭება — სივრცე არ უნდა იყოს `=`-ის გარშემო!
echo "$name"             # გამოყენება — ბრჭყალებში
echo "${name}_suffix"    # ფიგურული ფრჩხილები სიცხადისთვის

readonly PI=3.14         # უცვლადი
unset name               # წაშლა

# Environment ცვლადები (გადადის child პროცესებზე):
export PATH="$HOME/bin:$PATH"
env                      # ყველა env-ის ჩვენება
echo "$HOME $USER $PATH" # ხშირად გამოყენებული
```

---

### 🎒 არგუმენტები

| ცვლადი | მნიშვნელობა |
|--------|-------------|
| `$0` | სკრიპტის სახელი |
| `$1`, `$2`, ... | პოზიციური არგუმენტები |
| `$#` | არგუმენტების რაოდენობა |
| `$@` | ყველა არგუმენტი (ცალ-ცალკე, ბრჭყალებში) |
| `$*` | ყველა არგუმენტი (ერთი string-ი) |
| `$?` | წინა ბრძანების exit code |
| `$$` | მიმდინარე პროცესის PID |

```bash
#!/bin/bash
echo "სკრიპტი: $0"
echo "პირველი არგ: $1"
echo "სულ არგუმენტი: $#"
for arg in "$@"; do
    echo "  - $arg"
done
```

---

### 🔀 if / elif / else

```bash
if [[ "$1" == "hello" ]]; then
    echo "გამარჯობა!"
elif [[ -z "$1" ]]; then
    echo "არგუმენტი ცარიელია"
else
    echo "სხვა რამ: $1"
fi
```

ხშირი test-ები:

| ოპერატორი | მნიშვნელობა |
|-----------|-------------|
| `-eq`, `-ne`, `-lt`, `-le`, `-gt`, `-ge` | რიცხვების შედარება |
| `==`, `!=` | სტრიქონების შედარება (`[[ ]]`-ში) |
| `-z STR` | ცარიელია? |
| `-n STR` | არ არის ცარიელი? |
| `-f FILE` | ჩვეულებრივი ფაილია? |
| `-d DIR` | დირექტორიაა? |
| `-e PATH` | არსებობს? |
| `-r/-w/-x` | წაკითხვადი/ჩასაწერი/გასაშვებია? |

> 💡 ყოველთვის გამოიყენე `[[ ]]` `[ ]`-ის ნაცვლად bash-ში — უფრო უსაფრთხო და ფუნქციური.

---

### 🔄 ციკლები: for, while, until

```bash
# for over list
for fruit in apple banana cherry; do
    echo "$fruit"
done

# for over files
for f in *.log; do
    echo "Processing $f"
done

# C-style for
for ((i=0; i<5; i++)); do
    echo "$i"
done

# while
i=0
while (( i < 5 )); do
    echo "$i"
    ((i++))
done

# until — სანამ პირობა ცრუა
until ping -c1 -W1 example.com &>/dev/null; do
    echo "ვცდი..."
    sleep 2
done
```

---

### 🧰 ფუნქციები

```bash
greet() {
    local name="$1"           # local — სკოპი მხოლოდ ფუნქციაში
    echo "გამარჯობა, $name!"
    return 0                  # 0–255
}

greet "David"
greet "Nino"

# შედეგის "დაბრუნება" stdout-ით:
get_user_count() {
    wc -l < /etc/passwd
}
count=$(get_user_count)
echo "მომხმარებელი: $count"
```

---

### 🚦 Exit codes

```bash
ls /nonexistent
echo $?                # 2 — შეცდომა

# საკუთარი exit code:
if [[ -z "$1" ]]; then
    echo "გამოყენება: $0 <name>" >&2
    exit 1
fi

# ჯაჭვი && / || გავლით:
mkdir -p /tmp/foo && cd /tmp/foo || exit 1
command1 || echo "ვერ მოხერხდა"
```

ჩვეულებრივი codes: `0` წარმატება, `1` ზოგადი შეცდომა, `2` მცდარი გამოყენება, `126` ვერ შესრულდა, `127` ვერ მოიძებნა, `130` Ctrl+C-ით შეჩერდა.

---

### 📨 Redirection და pipes

```bash
command > file          # stdout → ფაილში (გადააწერს)
command >> file         # stdout → ფაილში (დაამატებს ბოლოს)
command 2> errors.log   # stderr → ფაილში
command &> all.log      # stdout+stderr ერთად
command 2>&1            # stderr → stdout-ში
command < input.txt     # stdin ფაილიდან

cmd1 | cmd2 | cmd3      # pipe — cmd1-ის stdout → cmd2-ის stdin
ps aux | grep python | wc -l

cmd > /dev/null 2>&1    # ყველაფრის ჩახშობა (Linux-ის "ნაგვის ყუთი")
```

---

### 🪄 Command substitution

```bash
today=$(date +%Y-%m-%d)        # ბრძანების შედეგის ცვლადში მოთავსება
echo "დღეს არის $today"

files=$(ls *.log | wc -l)
echo "ლოგ-ფაილების რაოდენობა: $files"

# ძველი სტილი (გათანაბარია, მაგრამ არ ჩაიდოს ერთმანეთში):
echo `date`
```

---

### 🛡️ Best practices: `set -euo pipefail`

ყოველი სერიოზული სკრიპტი იწყება ამით:

```bash
#!/bin/bash
set -euo pipefail
IFS=$'\n\t'
```

| დროშა | მნიშვნელობა |
|--------|-------------|
| `-e` | სკრიპტი ჩერდება პირველივე შეცდომაზე |
| `-u` | მცდარი ცვლადის გამოყენება → შეცდომა |
| `-o pipefail` | pipe-ის შიგნით შეცდომაც აღიქმება |

დამატებითი რეკომენდაცია: გამოიყენე [`shellcheck`](https://www.shellcheck.net/) თქვენი სკრიპტების შესამოწმებლად.

---

## 🎯 სავარჯიშოები

1. დაწერე სკრიპტი, რომელიც იღებს ფაილის გზას არგუმენტად და ბეჭდავს მის ხაზების რაოდენობას. თუ ფაილი არ არსებობს — შესაბამისი შეცდომა + `exit 1`.
2. შექმენი სკრიპტი, რომელიც გადის მიმდინარე დირექტორიის ყველა `.txt` ფაილზე და თითოეულის სარეზერვო ასლს ქმნის `.bak` სუფიქსით.
3. დაწერე ფუნქცია `is_prime`, რომელიც აბრუნებს 0-ს (true) თუ რიცხვი მარტივია.
4. გადაამოწმე ერთ-ერთი თქვენი სკრიპტი `shellcheck`-ის გავლით — გაასწორე ყველა გაფრთხილება.
