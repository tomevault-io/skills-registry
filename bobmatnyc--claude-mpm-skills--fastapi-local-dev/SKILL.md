---
name: fastapi-local-dev
description: FastAPI dev/prod runbook (Uvicorn reload, Gunicorn) Use when this capability is needed.
metadata:
  author: bobmatnyc
---

# FastAPI Local Dev

- Dev: `uvicorn app.main:app --reload`
- Imports: run from repo root; use `python -m uvicorn ...` or `PYTHONPATH=.`
- WSL: `WATCHFILES_FORCE_POLLING=true` if reload misses changes
- Prod: `gunicorn app.main:app -k uvicorn.workers.UvicornWorker -w <n> --bind :8000`

Anti-patterns:
- `--reload --workers > 1`
- PM2 `watch: true` for Python

References: `references/`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/bobmatnyc) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
