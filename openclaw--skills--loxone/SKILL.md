---
name: loxone
description: Control and monitor a Loxone Miniserver (smart home) via HTTP API and real-time WebSocket. Use for querying room/device status (temperatures, lights), watching live events, and sending safe control commands. Use when this capability is needed.
metadata:
  author: openclaw
---

# Loxone (Smart Home)

## Setup

See [SETUP.md](SETUP.md) for prerequisites and setup instructions.

## Commands
- `python3 scripts/loxone.py rooms`
- `python3 scripts/loxone.py map`
- `python3 scripts/loxone.py status "<Room>"`
- `python3 scripts/loxone.py control "<Room>" "<Control>" on|off`
- `python3 scripts/loxone_watch.py --room "<Room>" [--changes-only] [--duration <sec>]`

## Notes
- Treat as **read-only by default**; only use control commands when explicitly requested.
- WebSocket auth can be finicky; if WS fails, fall back to HTTP status queries.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
