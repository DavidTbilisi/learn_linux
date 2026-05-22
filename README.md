# 🐧 Linux-ის გზამკვლევი

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)
[![Deploy mdBook site](https://github.com/DavidTbilisi/learn_linux/actions/workflows/deploy.yml/badge.svg)](https://github.com/DavidTbilisi/learn_linux/actions/workflows/deploy.yml)
[![Lint](https://github.com/DavidTbilisi/learn_linux/actions/workflows/lint.yml/badge.svg)](https://github.com/DavidTbilisi/learn_linux/actions/workflows/lint.yml)

ქართულენოვანი გზამკვლევი Linux-ის ბრძანებებზე, მაგალითებითა და სავარჯიშოებით. სტრუქტურა მიყვება [roadmap.sh/linux](https://roadmap.sh/linux)-ს.

🌐 **საიტი:** <https://davidtbilisi.github.io/learn_linux/>

---

## 📚 როგორ გამოვიყენო

გზამკვლევი დაყოფილია თემატურ გვერდებად. რეკომენდებული თანმიმდევრობა დამწყებისთვის:

1. ჯერ ისწავლე **საფუძვლები** (ნავიგაცია → რედაქტირება → უფლებები → ძიება).
2. შემდეგ გადადი **სისტემაზე** (პროცესები, მომხმარებლები, systemd, პაკეტები, არქივები).
3. **ქსელის** ბლოკი (Network, SSH) — როცა საჭიროა server-ებთან მუშაობა.
4. **ავტომატიზაცია** (Bash scripting) — როცა აპირებ რუტინული ამოცანების სკრიპტებად გარდაქმნას.
5. ყოველი თემის ბოლოს ნახე **🎯 სავარჯიშოები** — ერთად [Exercises.md](./Exercises.md)-შიც.

> 💡 **რჩევა:** იქონიე ვირტუალური მანქანა ან Docker container ექსპერიმენტებისთვის (`docker run -it ubuntu bash`).

---

## 📖 სარჩევი

### საფუძვლები
- 📁 [ნავიგაცია](./Navigation.md) — `ls`, `cd`, `pwd`, `mkdir`, `rm`, `cp`, `mv`
- 🧑‍💻 [რედაქტირება](./Editing.md) — `nano`, `vim`, `cat`, `less`, `more`
- 🔐 [უფლებები](./Perms.md) — `chmod`, `chown`, `chgrp`, `umask`, setuid/setgid/sticky
- 🔍 [ძიება](./Search.md) — `grep`, `find`, `which`, `whereis`, `locate`

### სისტემა
- 🧪 [პროცესები](./Processes.md) — `ps`, `top`, `htop`, `kill`, `nice`, jobs
- 🧑‍🔧 [მომხმარებლები](./Users.md) — `useradd`, `usermod`, `passwd`, `sudo`
- 🖥️ [systemd](./Systemd.md) — `systemctl`, `journalctl`, timer-ები
- 📦 [პაკეტები](./Packages.md) — `apt`, `dnf`, `pacman`, snap/flatpak
- 📦 [არქივები](./Archives.md) — `tar`, `gzip`, `bzip2`, `xz`, `zip`

### ქსელი
- 🌐 [ქსელური ინსტრუმენტები](./Network.md) — `ping`, `curl`, `ip`, `ss`, `dig`, `nc`
- 🔐 [SSH](./SSH.md) — `ssh`, `scp`, `rsync`, `ssh-keygen`, tunneling

### ავტომატიზაცია
- 🐚 [Bash სკრიპტინგი](./Scripting.md) — ცვლადები, ციკლები, ფუნქციები, exit codes

### პრაქტიკა
- 🎯 [სავარჯიშოები](./Exercises.md) — დავალებები ყოველი თემისთვის + მინი-პროექტები

---

## 📚 დამატებითი რესურსები

- [roadmap.sh/linux](https://roadmap.sh/linux) — ვიზუალური roadmap
- [OverTheWire: Bandit](https://overthewire.org/wargames/bandit/) — wargame, ისწავლი თამაშით
- [Linux Journey](https://linuxjourney.com/) — ინტერაქტიული გაკვეთილები
- [The Linux Command Line (book, free)](https://linuxcommand.org/tlcl.php)
- [explainshell.com](https://explainshell.com/) — შენი ბრძანების ნაწილ-ნაწილ გაშიფვრა

---

## 🤝 წვლილის შეტანა

PR-ები მისასალმებელია! ნახე [CONTRIBUTING.md](./CONTRIBUTING.md) დეტალებისთვის. იდეებისთვის — [open issues](https://github.com/DavidTbilisi/learn_linux/issues) (`good first issue` ლეიბლი მონიშნავს ადვილ დავალებებს).

## 📄 ლიცენზია

[MIT](./LICENSE)
