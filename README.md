# HomeLab MkI

A small homelab built as a set of **microservices on a Raspberry Pi 5**. The Pi is too limited for a single large server, so the
plan is to run several small, lightweight services, each doing one job well, with **nginx** in front of all of them.

This is above all a **learning project**: networking, C, Rust, Linux administration and cybersecurity. I start by borrowing
existing software, and over time I want to replace pieces with my own code. There is no deadline.

## Hardware

| Part        | Details                                                                  |
|-------------|--------------------------------------------------------------------------|
| Board       | Raspberry Pi 5, 8 GB RAM, active cooler (headroom for overclocking)       |
| SD card     | 128 GB, **OS only** (Raspberry Pi OS Lite 64-bit)                         |
| External SSD| 1 TB+, permanently connected over USB 3, holds **all data**               |

**Why split storage?** SD cards wear out under constant writes. Databases, logs, metrics and the website all live on the SSD
(mounted at `/srv`), so the SD card mostly gets read. If the SD card dies, I reflash the OS and the data is still safe.

## Architecture

```
                 Internet / LAN
                       │  80 / 443
                ┌──────▼──────┐
                │    nginx    │  static files + reverse proxy + TLS
                └──────┬──────┘
        ┌──────────────┼───────────────┬──────────────┐
        │ /            │ /api/         │ /app/        │ /ws/
   static site    C services      Rust app server   socket server (C)
   (/srv/www)    (127.0.0.1)      (127.0.0.1)       (127.0.0.1)
                       │               │
                       └───────┬───────┘
                     SQLite → PostgreSQL, Redis, Mosquitto (MQTT)
```

Only nginx (and SSH) faces the network. Every other service listens on `127.0.0.1` and is reached through nginx.

## Services

| #  | Service               | Tech                                   | Built or borrowed            |
|----|-----------------------|----------------------------------------|------------------------------|
| 1  | Web server            | nginx + C                              | nginx borrowed, C mine       |
| 2  | App server            | Rust (my own engine)                   | Mine                         |
| 3  | Database              | SQLite first, then PostgreSQL          | Borrowed                     |
| 4  | Socket server         | C (probably)                           | Mine                         |
| 5  | Cache / storage       | Redis                                  | Borrowed                     |
| 6  | Messages and events   | Mosquitto (MQTT broker)                | Borrowed                     |
| 7  | Auth and security     | Argon2id + JWT                         | Mine                         |
| 8  | Metrics / monitoring  | Netdata, later Prometheus + Grafana    | Borrowed                     |

Automation and glue code: **Python** and **shell**.

## Repository layout

```
.
├── README.md          ← you are here
├── ROADMAP.md         ← the plan, phase by phase
├── NOTES.md           ← session log: what I did and what's next
├── docs/              ← architecture, deployment, ports, MQTT topics
├── deploy/
│   ├── nginx/         ← nginx config
│   └── systemd/       ← one .service unit per service
└── services/
    ├── web/           ← static website
    └── health/        ← first C service (src/, include/)
```

## Status

Phase 0: setting up the Pi and the repository. See [ROADMAP.md](ROADMAP.md).

## Future

When the Pi gets too small, move to a mini PC or a real server. Thanks to the microservice split, services can move one at a time.
