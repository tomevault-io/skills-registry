---
name: tilt-down
description: Stop ImmorTerm development environment with Tilt. Use this skill to gracefully stop services and clean up resources. ALWAYS use this instead of running `tilt down` directly. Use when this capability is needed.
metadata:
  author: lonormaly
---

# Tilt Down - Stop ImmorTerm Dev Environment

**CRITICAL**: NEVER run `pkill -f tilt` or any blanket kill! Always use the shutdown script. Multiple Tilt projects run in parallel.

## Usage

```bash
./scripts/tilt_down.sh
```

## Options

| Flag | Description |
|------|-------------|
| `--port PORT` | Override Tilt port (default: 10390) |

## What the Script Does

1. Reads saved PID from `.tilt.pid` (safe — targets only ImmorTerm's Tilt)
2. Falls back to finding process on port 10390 if PID file missing
3. Cleans up portless routes for `*.immorterm` services
4. Runs `tilt down` to clean up state
5. Shows recent errors/warnings from log

**Note**: The script does NOT stop the shared portless proxy (port 1355) — it's shared across all projects.

## Troubleshooting

**Script can't find the process**
```bash
# Find by port
lsof -ti:10390
```

**Orphaned portless routes**
```bash
portless list  # Check for stale *.immorterm routes
portless remove web.immorterm
portless remove api.immorterm
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/lonormaly) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
