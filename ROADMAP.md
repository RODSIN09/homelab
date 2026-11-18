# Roadmap

Author: Rod · Started: November 2026 · No deadline, the goal is to learn.

**Rule of thumb:** static first, then dynamic, then everything else. Each phase ends with something that **works on the
real Pi**. Every phase also has a **security** task, because learning to secure things is part of the point.

---

## MkI: core servers (web, app, database, socket)

### Phase 0: Foundations
Get the Pi and the repo ready before writing any service.

- [ ] Flash Raspberry Pi OS Lite (64-bit) to the SD card
- [ ] SSH with **key-only** login (disable password and root login)
- [ ] Format the SSD (ext4) and mount it at `/srv` through `/etc/fstab` using its UUID and `nofail`
- [ ] Create folders on the SSD: `/srv/www`, `/srv/data`, `/srv/logs`, `/srv/backups`
- [ ] Firewall (nftables or ufw): allow only 22, 80 and 443
- [ ] Automatic security updates (`unattended-upgrades`)
- [x] Fill in `.gitignore` so secrets, `.env` and `.db` files never get committed

> **Learn:** Linux users and permissions, fstab, SSH keys, firewalls.
> **Security:** this phase *is* security. A Pi with password SSH open to the internet gets brute-forced within hours.

### Phase 1: Static web server
- [ ] Install nginx and write my own `server {}` block (in `deploy/nginx/`)
- [ ] Serve the site from `/srv/www` (source in `services/web/`)
- [ ] Build a first real page (see *Website ideas* below)
- [ ] Deploy script: copy `services/web/` to `/srv/www` from the repo
- [ ] HTTPS on the LAN (self-signed certificate or `mkcert`)
- [ ] Security headers: `X-Content-Type-Options`, `Content-Security-Policy`, `server_tokens off`

> **Learn:** how nginx matches requests (`server`, `location`, `root`), MIME types, logs.
> **Security:** TLS, headers, hiding version numbers. Check the result with `curl -I` and `nmap`.

### Phase 2: First dynamic service, the C health check
Smallest possible end-to-end path: **browser → nginx → my C binary → response**.

- [ ] `services/health/src/main.c`: small HTTP server on `127.0.0.1:8080`
- [ ] `GET /api/health` answers `{"status":"ok","uptime":...}`
- [ ] nginx `location /api/ { proxy_pass http://127.0.0.1:8080; }`
- [ ] `Makefile`: `make`, `make install`, `make clean`
- [ ] `deploy/systemd/homelab_health.service`: starts at boot, restarts on crash
- [ ] Harden the unit: `DynamicUser=yes`, `ProtectSystem=strict`, `NoNewPrivileges=yes`

> **Learn:** sockets (`socket/bind/listen/accept`), parsing HTTP by hand, reverse proxies, systemd.
> **Security:** never trust input. Fuzz the parser, test with `-fsanitize=address`, try malformed requests.

### Phase 3: Database
- [ ] SQLite first (one file in `/srv/data`, nothing to configure) to learn the basics
- [ ] Then PostgreSQL with its data directory on the SSD, listening on localhost only
- [ ] One user per service, with the minimum privileges it needs
- [ ] Automatic backups to `/srv/backups` (`pg_dump` + cron or a systemd timer)

> **Security:** SQL injection (always use prepared statements), least privilege, testing that backups actually restore.

### Phase 4: App server (Rust)
- [ ] Put my Rust engine behind nginx (`/app/`)
- [ ] Host my projects and games on it
- [ ] Connect it to the database

### Phase 5: Socket server (C)
- [ ] WebSocket or raw TCP server for real-time features (`/ws/` through nginx)
- [ ] Possible uses: live status page, multiplayer for the games, chat

### Phase 6: Connect everything
- [ ] Static site links to and uses the app, the database and the socket server through nginx
- [ ] Write `docs/architecture.md` and `docs/ports.md` from what actually exists
- [ ] Turn static pages into dynamic ones, a little at a time

---

## MkII: supporting services

- [ ] **Redis:** cache and sessions (localhost only, password set)
- [ ] **Mosquitto (MQTT):** events between services and IoT devices; TLS + users, no anonymous access. Topics go in `docs/mqtt-topics.md`
- [ ] **Auth service:** Argon2id password hashing + JWT. A great way to learn, but only exposed after a lot of testing
- [ ] **Monitoring:** Netdata first (light), then Prometheus + node_exporter + Grafana
- [ ] **Remote access:** WireGuard or Tailscale VPN instead of opening ports on the router
- [ ] **Off-device backups** (the SSD can fail too)

## MkIII and beyond
- [ ] Replace borrowed pieces with my own code where it makes sense
- [ ] Overclocking, only after there is a stable baseline and monitoring to compare against
- [ ] Move to a mini PC or a real server

---

## Planned ports

| Service       | Port        | Bound to   |
|---------------|-------------|------------|
| SSH           | 22          | LAN        |
| nginx         | 80 / 443    | LAN / WAN  |
| C health      | 8080        | 127.0.0.1  |
| Rust app      | 8081        | 127.0.0.1  |
| Socket server | 9000        | 127.0.0.1  |
| PostgreSQL    | 5432        | 127.0.0.1  |
| Redis         | 6379        | 127.0.0.1  |
| Mosquitto     | 1883 / 8883 | LAN (TLS)  |
| Netdata       | 19999       | 127.0.0.1  |

## Website ideas (open question)

- **Homelab devlog:** publish NOTES and the roadmap as the website itself
- **Portfolio:** a page for each of my projects
- **Status page:** static at first, later fed live by `/api/health`
- **Browser games:** compile my C games to WebAssembly with Emscripten and serve them as static files
- **PHP?** Not needed, because the dynamic parts are C and Rust. Could still be worth trying once through PHP-FPM (FastCGI) to
  compare it with `proxy_pass`
