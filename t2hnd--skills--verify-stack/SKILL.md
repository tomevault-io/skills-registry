---
name: verify-stack
description: > Use when this capability is needed.
metadata:
  author: t2hnd
---

# Verify Stack Skill

Verify full-stack integration. Catches "Integration and Configuration Gaps" before reporting a feature as complete.

## Workflow

### 1. Detect Project Stack
- Check for `package.json` (frontend), `requirements.txt` / `pyproject.toml` (backend)
- Check for `vite.config.ts` (Vite), identify backend framework (FastAPI, Express, etc.)

### 2. Backend Health Check
- Check if server process is running on expected ports (e.g., `lsof -i :8000`)
- If NOT running, attempt to start it
- Wait up to 10 seconds for server to be ready
- If still not responding, **REPORT ERROR** with startup logs

### 3. Vite Proxy Configuration Check
- Read `vite.config.ts` / `vite.config.js`
- Extract proxy configuration from `server.proxy`
- Search frontend code for API calls (`fetch`, `axios`)
- Compare found API routes with proxy config
- **Report missing routes** (do NOT auto-edit vite.config)

### 4. Environment Variables Check
- Check if `.env` file exists
- Verify required env variables are set
- If `.env.example` exists, compare for missing keys
- Report missing dependencies (e.g., `python-dotenv`)

### 5. API Endpoint Smoke Test
- Identify API endpoints from backend code
- Test each with `curl`, report status codes:
  - ✓ 200/201: OK
  - ⚠ 404: Route not found (possible proxy issue)
  - ⚠ 500: Server error
  - ✗ Connection refused: Server not running

### 6. Frontend Dev Server Check
- Check if Vite dev server is running
- If running, verify it serves correctly
- If not running, **warn** but don't auto-start

### 7. End-to-End Request Path Test
- If both frontend and backend are running:
  - Make a test request through the full stack (frontend → proxy → backend)
  - Verify response is from backend (not 404 from Vite)

### 8. Report Summary
```
✓ Backend: FastAPI running on :8000
✓ Vite Proxy: Configured for /api/*
✓ Endpoints: 4/4 responding
✗ Missing: .env variable API_KEY
⚠ Frontend dev server not running
```

## Important Rules

- **NEVER assume services are running** — always verify
- **NEVER auto-start frontend** — only backend services
- **NEVER auto-edit vite.config** — report issues instead
- **DO verify the full request path**: frontend → proxy → backend → response
- **DO provide actionable next steps** with specific commands

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/t2hnd) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
