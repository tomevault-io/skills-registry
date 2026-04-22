---
name: ralph-status
description: Show current Ralph Ultra project status including PRD progress, loop state, skill results, and session history. Use to check how the autonomous development is progressing. Use when this capability is needed.
metadata:
  author: kimhons
---

## Ralph Ultra Status

Display comprehensive project status.

### What this shows

1. **Project info** — Name, type, template, security mode
2. **PRD progress** — Stories by status (done/in-progress/pending/blocked)
3. **Current session** — Iteration count, elapsed time, cost estimate
4. **Skill results** — Last run results for each skill
5. **Recent activity** — Last 10 log entries
6. **Health** — Overall project health score

### Usage

```
/ralph-ultra:ralph-status
```

### Status reads from

- `.ralph-ultra/config.json` — Project configuration
- `prd.json` — Story progress
- `.ralph-ultra/sessions/current/` — Active session data
- `.ralph-ultra/cache/` — Cached skill results

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kimhons) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
