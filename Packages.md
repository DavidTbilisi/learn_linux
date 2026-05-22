# 📦 პაკეტების მართვა

Linux-ის ერთ-ერთი ფუნდამენტური სხვაობა Windows/macOS-ისგან — **პაკეტური მენეჯერი**: ერთიანი ხელსაწყო პროგრამების ინსტალაციისთვის, განახლებისთვის და წაშლისთვის. ყოველ დისტრიბუციას აქვს თავისი მენეჯერი.

## სარჩევი
- [`apt` (Debian / Ubuntu)](#-apt-debian--ubuntu)
- [`dnf` / `yum` (Fedora / RHEL / CentOS)](#-dnf--yum-fedora--rhel--centos)
- [`pacman` (Arch)](#-pacman-arch)
- [უნივერსალური ფორმატები: `snap`, `flatpak`, `AppImage`](#-უნივერსალური-ფორმატები)
- [შედარების ცხრილი](#-შედარების-ცხრილი)
- [სავარჯიშოები](#-სავარჯიშოები)

---

### 📦 `apt` (Debian / Ubuntu)

```bash
sudo apt update                  # პაკეტების სიის განახლება (აუცილებელია upgrade-მდე)
sudo apt upgrade                 # ყველა პაკეტის განახლება
sudo apt full-upgrade            # + dependency-ების სრული გადაწყვეტა

sudo apt install nginx           # ინსტალაცია
sudo apt install nginx curl git  # რამდენიმე ერთად
sudo apt remove nginx            # წაშლა (config-ი რჩება)
sudo apt purge nginx             # წაშლა + config-ი
sudo apt autoremove              # არასაჭირო dependency-ების გასუფთავება

apt search "web server"          # ძიება
apt show nginx                   # დეტალები პაკეტზე
apt list --installed             # დაყენებული პაკეტები
```

> 💡 ფაილების მართვისთვის `apt`-ის ქვემოთ მუშაობს `dpkg`: `dpkg -i package.deb`, `dpkg -l`.

---

### 🎩 `dnf` / `yum` (Fedora / RHEL / CentOS)

`dnf` — `yum`-ის თანამედროვე ჩამნაცვლებელი (იგივე სინტაქსი).

```bash
sudo dnf check-update            # ხელმისაწვდომი განახლებები
sudo dnf upgrade                 # ყველაფრის განახლება
sudo dnf install httpd
sudo dnf remove httpd
sudo dnf autoremove

dnf search nginx
dnf info httpd
dnf list installed
sudo dnf clean all               # cache-ის გასუფთავება
```

ფაილების მართვისთვის: `rpm -ivh package.rpm`, `rpm -qa`.

---

### 🏛️ `pacman` (Arch)

Arch-ის ფილოსოფია: rolling release, მინიმალიზმი.

```bash
sudo pacman -Syu                 # სრული განახლება (sync + upgrade)
sudo pacman -S nginx             # ინსტალაცია
sudo pacman -R nginx             # წაშლა
sudo pacman -Rns nginx           # წაშლა + dependency-ები + config
sudo pacman -Ss "web server"     # ძიება
pacman -Q                        # დაყენებული პაკეტები
pacman -Qi nginx                 # ინფო
```

> AUR (Arch User Repository) - `yay` ან `paru` helper-ით: `yay -S package`.

---

### 🌍 უნივერსალური ფორმატები

ეს ფორმატები მუშაობს ნებისმიერ დისტრიბუციაზე:

**Snap** (Canonical-ის სტანდარტი):
```bash
sudo snap install code           # VS Code
snap list
sudo snap remove code
```

**Flatpak** (community-driven, sandboxed):
```bash
flatpak install flathub org.mozilla.firefox
flatpak list
flatpak run org.mozilla.firefox
```

**AppImage** (single-file, no install):
```bash
chmod +x myapp.AppImage
./myapp.AppImage
```

---

### 📊 შედარების ცხრილი

| მოქმედება | apt | dnf | pacman |
|-----------|-----|-----|--------|
| განახლება | `apt update && apt upgrade` | `dnf upgrade` | `pacman -Syu` |
| ინსტალაცია | `apt install pkg` | `dnf install pkg` | `pacman -S pkg` |
| წაშლა | `apt remove pkg` | `dnf remove pkg` | `pacman -R pkg` |
| ძიება | `apt search pkg` | `dnf search pkg` | `pacman -Ss pkg` |
| ინფო | `apt show pkg` | `dnf info pkg` | `pacman -Qi pkg` |
| ფაილი | `dpkg -L pkg` | `rpm -ql pkg` | `pacman -Ql pkg` |

---

## 🎯 სავარჯიშოები

1. შენი დისტრიბუციის ოფიციალურ რეპოზიტორიაში მოძებნე პროგრამა `htop` — დააყენე და გადაამოწმე ვერსია.
2. ნახე ბოლო რა პაკეტები განახლდა შენს სისტემაში (`/var/log/apt/history.log` ან `dnf history`).
3. გაარკვიე, რომელი პაკეტი შეიცავს ფაილს `/etc/nginx/nginx.conf` (`dpkg -S`, `rpm -qf`, `pacman -Qo`).
4. დააყენე ერთი და იგივე პროგრამა Snap-ით და native პაკეტ-მენეჯერით — შეადარე ზომა და start-time.
