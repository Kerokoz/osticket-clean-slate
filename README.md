# 🎫 osTicket Clean Slate — Dockerized, Multi-Architecture Help Desk

A clean, installer-fresh, **multi-architecture** Docker image of [osTicket](https://osticket.com) 1.18.3 — wired to MySQL via Docker Compose and ready to run in minutes.

![osTicket](https://img.shields.io/badge/osTicket-1.18.3-orange)
![PHP](https://img.shields.io/badge/PHP-8.2-blue)
![Apache](https://img.shields.io/badge/Apache-2.4-red)
![Platforms](https://img.shields.io/badge/platforms-amd64%20%7C%20arm64-green)
![IMAP](https://img.shields.io/badge/email-IMAP%20enabled-brightgreen)

> 🐳 **Available on Docker Hub:** [kerokoz/osticket-clean-slate](https://hub.docker.com/r/kerokoz/osticket-clean-slate) — the Docker Hub page has the detailed, step-by-step setup particulars (quick start, installer settings, and post-install hardening). Pull it with:
> ```bash
> docker pull kerokoz/osticket-clean-slate:1.18.3
> ```

---

## ✅ Requirements

- **Docker** and **Docker Compose** installed — the easiest way is [Docker Desktop](https://www.docker.com/products/docker-desktop/), which includes both.
- An internet connection for the first image pull.
- Any operating system that runs Docker — Windows, macOS (Intel or Apple Silicon), or Linux.

---

## 📌 What is osTicket?

[osTicket](https://osticket.com) is a widely-used, open-source **support ticket system**. It turns incoming requests (via web forms or email) into organized tickets that support staff can track, assign, and respond to — a lightweight, self-hosted help desk used by businesses, schools, and organizations of all sizes.

This repository provides a **Dockerized, ready-to-run** version of osTicket: pull the image, start it with Docker Compose, and complete the web installer in your browser. It ships with the installer intact so each deployment sets up its own fresh database.

---

## ✨ Features

- 🧱 Built on the official `php:8.2-apache` base image.
- 🌍 **Multi-architecture** — runs natively on `linux/amd64` (Intel/AMD) and `linux/arm64` (Apple Silicon / ARM). Docker pulls the right version automatically.
- 🐳 **Docker Compose orchestration** — the app and a MySQL 8.0 database start together, with a persistent volume so data survives restarts.
- 🔐 **Credentials via environment variables** — no secrets baked into the image.
- 📧 **IMAP-enabled** — the PHP `imap` extension is included, so osTicket can fetch incoming email and convert it into tickets.
- 🆕 **Installer-fresh** — the web installer is included for a clean first-run setup.

---

## 🧩 Dependencies

| Component | Version / Detail |
|-----------|------------------|
| Base image | `php:8.2-apache` |
| Web server | Apache 2.4 |
| PHP | 8.2 |
| PHP extensions | `gd`, `intl`, `mysqli`, `zip`, `imap` |
| Database | MySQL 8.0 (run as a companion container) |
| Orchestration | Docker Compose |
| Architectures | `linux/amd64`, `linux/arm64` |

---

## 🚀 Quick start

Save the following as `docker-compose.yml` (change the passwords first):

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

Start it:

```bash
docker compose up -d
```

Open **http://localhost:8080** and complete the installer.

### Installer database settings
| Field | Value |
|-------|-------|
| MySQL Database | `osticket` |
| MySQL Username | `osticket_user` |
| MySQL Password | _your `MYSQL_PASSWORD`_ |
| MySQL Hostname | `db` |

> Use hostname **`db`** (the Compose service name), not `localhost`.

---

## 🔒 After installing — recommended hardening

osTicket shows two reminder banners after setup. Apply both on your running instance:

```bash
# Remove the installer
docker compose exec app rm -rf /var/www/html/setup

# Lock the config file to read-only
docker compose exec app chmod 644 /var/www/html/include/ost-config.php
```

These apply to the running container and reset if it's recreated — by design, so the image stays installer-ready for the next deployment.

---

## 🏷️ Image tags

| Tag | Meaning |
|-----|---------|
| `1.18.3` | Pinned version |
| `latest` | Most recent build |

---

## 📄 License & credits

osTicket is open-source software released under the GPL. This repository packages and documents a Dockerized deployment of it — all credit for osTicket itself goes to its maintainers.

- **Official osTicket project:** [osTicket/osTicket](https://github.com/osTicket/osTicket)
- **osTicket website:** [osticket.com](https://osticket.com)

Maintained by **kerokoz**.
