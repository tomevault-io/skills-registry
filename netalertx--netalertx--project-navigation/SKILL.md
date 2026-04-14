---
name: project-navigation
description: Reference for the NetAlertX codebase structure, key file paths, and configuration locations. Use this when exploring the codebase or looking for specific components like the backend entry point, frontend files, or database location. Use when this capability is needed.
metadata:
  author: netalertx
---

# Project Navigation & Structure

## Codebase Structure & Key Paths

- **Source Code:** `/workspaces/NetAlertX` (mapped to `/app` in container via symlink).
- **Backend Entry:** `server/api_server/api_server_start.py` (Flask) and `server/__main__.py`.
- **Frontend:** `front/` (PHP/JS).
- **Plugins:** `front/plugins/`.
- **Config:** `/data/config/app.conf` (runtime) or `back/app.conf` (default).
- **Database:** `/data/db/app.db` (SQLite).

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/netalertx) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
