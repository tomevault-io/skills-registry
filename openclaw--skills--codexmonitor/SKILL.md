---
name: codexmonitor
description: > Use when this capability is needed.
metadata:
  author: openclaw
---

# codexmonitor

Use `codexmonitor` to browse local OpenAI Codex sessions.

## Setup

See [SETUP.md](SETUP.md) for prerequisites and setup instructions.

## Common commands

- List sessions (day): `codexmonitor list 2026/01/08`
- List sessions (day, JSON): `codexmonitor list --json 2026/01/08`
- Show a session: `codexmonitor show <session-id>`
- Show with ranges: `codexmonitor show <session-id> --ranges 1...3,26...28`
- Show JSON: `codexmonitor show <session-id> --json`
- Watch all: `codexmonitor watch`
- Watch specific: `codexmonitor watch --session <session-id>`

## Notes
- Sessions live under `~/.codex/sessions/YYYY/MM/DD/` by default.
- If your sessions live somewhere else, set `CODEX_SESSIONS_DIR` (preferred) or `CODEX_HOME`.
- Sessions can be resumed/appended by id via Codex: `codex exec resume <SESSION_ID> "message"`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
