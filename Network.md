# 🌐 ქსელური ინსტრუმენტები

## სარჩევი
- [`ping` — კავშირის ტესტი](#-ping--კავშირის-ტესტი)
- [`curl` / `wget` — HTTP მოთხოვნები](#-curl--wget--http-მოთხოვნები)
- [`ip` / `ifconfig` — ქსელური ინტერფეისები](#-ip--ifconfig--ქსელური-ინტერფეისები)
- [`ss` / `netstat` — გახსნილი პორტები](#-ss--netstat--გახსნილი-პორტები)
- [`dig` / `nslookup` / `host` — DNS](#-dig--nslookup--host--dns)
- [`traceroute` / `mtr` — მარშრუტი](#-traceroute--mtr--მარშრუტი)
- [`nc` — netcat](#-nc--netcat)
- [სავარჯიშოები](#-სავარჯიშოები)

---

### 🏓 `ping` – კავშირის ტესტი

```bash
ping google.com           # უწყვეტი ping
ping -c 4 google.com      # მხოლოდ 4 პაკეტი
ping -i 0.2 192.168.1.1   # ინტერვალი 0.2 წმ
ping6 ipv6.google.com     # IPv6
```

---

### 🌍 `curl` / `wget` – HTTP მოთხოვნები

**`curl`** — უნივერსალური HTTP კლიენტი (GET, POST, header-ები, auth).

```bash
curl https://example.com                  # GET
curl -I https://example.com               # მხოლოდ header-ები
curl -L https://short.url                 # follow redirect-ები
curl -o page.html https://example.com     # ფაილში შენახვა
curl -X POST -d '{"k":"v"}' \
     -H "Content-Type: application/json" \
     https://api.example.com/users        # POST JSON-ით
curl -u user:pass https://api.example.com # basic auth
```

**`wget`** — ფაილების ჩამოტვირთვა (recursive mirror-ისთვის უკეთესია).

```bash
wget https://example.com/file.zip
wget -c https://example.com/big.iso       # შეწყვეტილი ჩამოტვირთვის გაგრძელება
wget -r -np https://example.com/docs/     # რეკურსიული, parent-ის გარეშე
```

---

### 🔌 `ip` / `ifconfig` – ქსელური ინტერფეისები

`ifconfig` მოძველებულია; თანამედროვე სტანდარტი — `ip`.

```bash
ip a                       # ყველა ინტერფეისი და მისამართი (short: ip addr)
ip r                       # routing table (short: ip route)
ip link                    # ფიზიკური ინტერფეისები
ip a show eth0             # კონკრეტული ინტერფეისი

sudo ip link set eth0 up   # ინტერფეისის ჩართვა
sudo ip addr add 192.168.1.50/24 dev eth0
sudo ip route add default via 192.168.1.1
```

---

### 🚪 `ss` / `netstat` – გახსნილი პორტები და სოკეტები

`ss` უფრო სწრაფი და თანამედროვე ალტერნატივაა `netstat`-ის.

```bash
ss -tuln                   # TCP+UDP, listening, ციფრულად (პორტი/IP არ resolve-დება)
ss -tlnp                   # + პროცესის სახელი (sudo სასარგებლოა)
ss -s                      # სტატისტიკა
ss -t state established    # მხოლოდ აქტიური TCP კავშირები

# ძველი ალტერნატივა:
netstat -tlnp
```

---

### 🔎 `dig` / `nslookup` / `host` – DNS

```bash
dig example.com                # A ჩანაწერი
dig example.com MX             # ფოსტა
dig example.com ANY            # ყველაფერი
dig @8.8.8.8 example.com       # კონკრეტული DNS სერვერი
dig +short example.com         # მხოლოდ პასუხი

host example.com
nslookup example.com
```

---

### 🛣️ `traceroute` / `mtr` – მარშრუტი

```bash
traceroute google.com          # hop-ების სია სამიზნემდე
traceroute -n google.com       # IP-ები, DNS-ის გარეშე
mtr google.com                 # ცოცხალი traceroute + ping ერთად (sudo apt install mtr)
```

---

### 🔌 `nc` – netcat

"Swiss army knife" ქსელისთვის — სოკეტების გახსნა, პორტის ტესტი, ფაილების გადატანა.

```bash
nc -zv example.com 443         # პორტის ღიაობის ტესტი
nc -lvnp 4444                  # listening server პორტ 4444-ზე
echo "hello" | nc host 9000    # სოკეტში მონაცემის გაგზავნა

# ფაილის გადატანა (მიმღების მხარე):
nc -l 5555 > received.zip
# გამგზავნის მხარე:
nc host 5555 < send.zip
```

---

## 🎯 სავარჯიშოები

1. გადაამოწმე, რომელი პროცესი იყენებს პორტ 80-ს შენს მანქანაზე.
2. იპოვე `github.com`-ის ყველა MX (ფოსტის) ჩანაწერი.
3. ნახე, ვინ პასუხობს `8.8.8.8`-ის ping-ზე და რა მარშრუტი მიდის იქამდე.
4. ჩამოწერე `https://example.com`-ის header-ები `curl`-ით.
5. გაუშვი `nc`-ის გავლით პატარა ჩათი ორ ტერმინალს შორის.
