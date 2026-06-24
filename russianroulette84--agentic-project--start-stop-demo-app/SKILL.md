---
name: startstop-demo-app
description: Start or stop the demo server (FastAPI) on port 2026. Use when you need to run or kill the development server. Use when this capability is needed.
metadata:
  author: RussianRoulette84
---

# Start/Stop Demo App

Manage the demo FastAPI development server running on port 2026.

## Tools

- `tools/start.py` - Start the demo server (`src/server/main.py`)
- `tools/stop.py` - Stop the server running on port 2026

## Usage

```bash
# Start the app
python3 .claude/skills/start-stop-app/tools/start.py

# Stop the app
python3 .claude/skills/start-stop-app/tools/stop.py
```

---
> Source: [RussianRoulette84/agentic_project](https://github.com/RussianRoulette84/agentic_project) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-22 -->
