---
name: tootoo
description: Sync your TooToo codex and monitor agent alignment with your values Use when this capability is needed.
metadata:
  author: openclaw
---

# TooToo

Syncs your personal codex from TooToo and monitors alignment.

## Commands
- `/tootoo setup <username>` — Initial setup: fetch codex, generate SOUL.md
- `/tootoo sync` — Force re-sync codex from TooToo
- `/tootoo report` — Generate alignment report for recent conversations
- `/tootoo status` — Show current alignment stats

## Configuration
Set your TooToo username in openclaw.json:
```json5
{ skills: { entries: { "tootoo": { username: "your-username" } } } }
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
