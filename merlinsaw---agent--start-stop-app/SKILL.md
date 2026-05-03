---
name: startstop-app
description: Start or stop the user-management FastAPI app. Use when you need to tun or kill the dev server. Use when this capability is needed.
metadata:
  author: merlinsaw
---

# Start/Stop App

## Tool

- tools/start.py - Start the app with uvicorn on port 8000
- tools/stop.py - Kill any running uvicorn process

## Usage

```bash
# Start the app
uv run .agent/skills/start-stop-app/tools/start.py

# Stop the app
uv run .agent/skills/start-stop-app/tools/stop.py
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/merlinsaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
