---
name: start-repos
description: Starts the Forex Trading Bot backend and frontend repositories in iTerm split panes. Use when this capability is needed.
metadata:
  author: hbarefoot
---

# Start Repos

This skill starts the development environment for the Forex Trading Bot project.

## Capabilities
- Launches iTerm with split panes.
- Starts the FastAPI backend (uvicorn) in one pane.
- Starts the React frontend (Vite) in the other pane.

## Usage
To start the repositories, simply run the `start_project.sh` script located in the project root.

```bash
./start_project.sh
```

## Prerequisites
- iTerm2 must be installed.
- The `start_project.sh` script must be executable (`chmod +x start_project.sh`).
- Python virtual environment (`backend/venv`) should be set up.
- Node modules (`frontend/node_modules`) should be installed.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hbarefoot) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
