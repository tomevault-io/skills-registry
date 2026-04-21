---
name: functions-local-dev
description: Runs and troubleshoots the Azure Functions backend locally (build/watch/func host start, local.settings modes). Use when developing or debugging backend HTTP/queue functions.
metadata:
  author: piquet-h
---

# Functions local dev

Use this skill when you are asked to:

- Start the backend Functions host locally.
- Switch local persistence modes (memory vs cosmos).
- Debug common “func won’t start” / config issues.

## Canonical local loop

Preferred options:

1. VS Code tasks (recommended):
    - `npm watch (functions)`
    - `func: host start`
2. Single command dev loop:
    - `npm run dev` in `backend/`

## Helper scripts

- `scripts/start-backend-dev.mjs` — starts backend dev loop with a stable entrypoint.

## Environment notes

- Backend expects `local.settings.json` in `backend/`.
- Use the existing npm helpers:
    - `npm run use:memory` (backend)
    - `npm run use:cosmos` (backend)

If Azure Functions Core Tools (`func`) isn’t installed, the host start step will fail; prefer VS Code task output for diagnosis.

---

Last reviewed: 2026-01-15

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/piquet-h) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
