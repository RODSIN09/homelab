# Session notes

Author: Rod · October 2026 onwards

Short log of each work session: what got done, what was decided, what's next. The long-term plan lives in [ROADMAP.md](ROADMAP.md).

---

## Session 1
- **To do:** organise the files, design the logical structure of the server and decide what each part does.

## Session 2
- **Done:** session 1 tasks.
- **Done:** SSH installed.
- **Decision:** how nginx talks to my C code. Two options:
  1. **FastCGI**: nginx speaks the FastCGI protocol to my program.
  2. **Plain HTTP on a local port**: my C binary listens on `127.0.0.1:8080` and nginx proxies to it.

  → Going with **option 2**: it's simpler and easier to debug (I can test it with `curl` directly).
- **Request flow:**
  - Outside requests come in on **80/443**.
  - Static file (html, css, js, images) → nginx serves it straight from disk.
  - Dynamic request (e.g. `/api/data`) → nginx proxies it to the C binary running in the background.
- **Next:** organise the files, prepare nginx, prepare the deploy setup.

## Session 3 (2026-11-18)
- **Done:** rewrote README and ROADMAP; the roadmap is now split into phases (static → dynamic → the rest).
- **Decision:** SD card = OS only. External SSD (1 TB+) mounted at `/srv` holds all data.
- **Moved:** `docs/include/backend.h` → `services/health/include/backend.h`.
- **Next:** Phase 0 (SSH key-only, mount the SSD, firewall, `.gitignore`).
