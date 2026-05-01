---
name: port-check
description: Check if services are responding on given host:port pairs. Supports TCP and HTTP checks with configurable timeout. Use for service monitoring, health checks, and network debugging. Use when this capability is needed.
metadata:
  author: openclaw
---

# Port Check Skill

Quickly verify if services are up and responding on specific ports.

## Usage

```bash
# Basic TCP check
bash scripts/port-check.sh localhost:8080 localhost:5432

# Multiple targets with HTTP status check
bash scripts/port-check.sh localhost:80 api.example.com:443 --http

# Custom timeout (default 3s)
bash scripts/port-check.sh 192.168.1.1:22 --timeout 5
```

## Output
- ✅ `host:port — open` (TCP connected)
- ✅ `host:port — open (HTTP 200)` (with --http flag)
- ⚠️ `host:port — open but HTTP 500` (port open, bad HTTP status)
- ❌ `host:port — closed/timeout` (no response)

## Exit Codes
- `0` — all targets up
- `1` — one or more targets down

## Common Checks
```bash
# OpenClaw gateway
bash scripts/port-check.sh localhost:18789 --http

# Database + web stack
bash scripts/port-check.sh localhost:5432 localhost:6379 localhost:3000

# Home network devices
bash scripts/port-check.sh 192.168.1.1:80 192.168.1.50:22 --timeout 2
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
