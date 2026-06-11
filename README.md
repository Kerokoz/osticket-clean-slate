# 🎫 osTicket Clean Slate — Dockerized, Multi-Architecture Help Desk

A clean, installer-fresh, **multi-architecture** Docker image of [osTicket](https://osticket.com) 1.18.3 — built from scratch, wired to MySQL via Docker Compose, published to Docker Hub, and running a live help desk with authenticated email delivery.

![osTicket](https://img.shields.io/badge/osTicket-1.18.3-orange)
![PHP](https://img.shields.io/badge/PHP-8.2-blue)
![Apache](https://img.shields.io/badge/Apache-2.4-red)
![Platforms](https://img.shields.io/badge/platforms-amd64%20%7C%20arm64-green)
![IMAP](https://img.shields.io/badge/email-IMAP%20enabled-brightgreen)
![Docker Hub](https://img.shields.io/badge/Docker%20Hub-kerokoz%2Fosticket--clean--slate-blue)

> **Pull it:** `docker pull kerokoz/osticket-clean-slate:1.18.3`
> **Docker Hub:** https://hub.docker.com/repository/docker/kerokoz/osticket-clean-slate

---

## 📌 What this is

A ready-to-run osTicket image that anyone — on Windows, Intel Mac, Apple Silicon, or Linux — can pull and stand up in minutes. It ships with the web installer intact so each user completes a fresh setup against their own database. Built deliberately and documented end to end, including the real failures and how they were solved.

**Highlights:**
- 🧱 Built from a `php:8.2-apache` base with the PHP extensions osTicket needs (`gd`, `intl`, `mysqli`, `zip`, `imap`).
- 🌍 **Multi-architecture** (`linux/amd64` + `linux/arm64`) — runs natively on any common chip.
- 🐳 Orchestrated with **Docker Compose** (app + MySQL 8.0 + persistent volume), credentials passed as environment variables.
- 📧 **IMAP-enabled** for inbound email-to-ticket, with a documented, authenticated **SMTP** outbound setup.
- 📚 Fully documented build journey — five playbooks covering build, publish, multi-arch, and operations.

---

## 🚀 Quick start

Save as `docker-compose.yml` (change the passwords):

```yaml
services:
  db:
    image: mysql:8.0
    environment:
      MYSQL_ROOT_PASSWORD: changeme_root
      MYSQL_DATABASE: osticket
      MYSQL_USER: osticket_user
      MYSQL_PASSWORD: changeme_pass
    volumes:
      - osticket_db_data:/var/lib/mysql
  app:
    image: kerokoz/osticket-clean-slate:1.18.3
    depends_on: [db]
    ports: ["8080:80"]
volumes:
  osticket_db_data:
```

```bash
docker compose up -d
```

Open **http://localhost:8080** and complete the installer. On the database screen use hostname **`db`** (not `localhost`), database `osticket`, user `osticket_user`, and your chosen password.

### 🔒 After installing — hardening (do both)
```bash
docker compose exec app rm -rf /var/www/html/setup
docker compose exec app chmod 644 /var/www/html/include/ost-config.php
```

---

## 🧩 Technical decisions worth noting

- **Clean image keeps `setup/`.** A shareable installer image must retain the web installer; hardening (removing `setup/`, locking the config) is done per-deployment, not baked in. Clean-image vs production-hardened are deliberately different states.
- **Secrets via environment variables**, never hard-coded into the image — so the published image stays password-free and shareable.
- **`imap` added via a maintained extension installer**, after the deprecated Debian package broke the initial build — then rebuilt multi-arch so every user benefits.
- **Authenticated SMTP for deliverability.** Outbound email runs through a properly domain-authenticated relay (SPF/DKIM), after an unauthenticated server got mail flagged as spam. Deliverability is treated as a first-class requirement, not an afterthought.

---

## 📦 Image details

- **Base:** `php:8.2-apache`
- **PHP extensions:** `gd`, `intl`, `mysqli`, `zip`, `imap`
- **Architectures:** `linux/amd64`, `linux/arm64`
- **Tags:** `1.18.3` (pinned), `latest`
- **Companion service:** MySQL 8.0

---

## 🛠️ Everyday commands

```bash
docker compose up -d        # start (app + db)
docker compose ps           # status
docker compose stop         # pause
docker compose start        # resume
docker compose down         # stop + remove containers (keeps data)
docker compose down -v      # also wipe data (full reset)
docker compose pull         # fetch the latest image
```
> Run `docker compose` commands from the folder containing `docker-compose.yml`.

---

## 📄 License & credits

osTicket is open-source software released under the GPL. This repository packages and documents a Dockerized deployment of it — all credit for osTicket itself goes to its maintainers.

- **Official osTicket project:** [osTicket/osTicket](https://github.com/osTicket/osTicket)
- **osTicket website:** [osticket.com](https://osticket.com)

Dockerized, packaged, and maintained by **[@Kerokoz](https://github.com/Kerokoz)** · Docker Hub: **[kerokoz/osticket-clean-slate](https://hub.docker.com/r/kerokoz/osticket-clean-slate)**
