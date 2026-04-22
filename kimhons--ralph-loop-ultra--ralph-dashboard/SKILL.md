---
name: ralph-dashboard
description: Open the live terminal dashboard showing real-time loop progress, story status grid, cost tracking, and skill results. Use while the autonomous loop is running. Use when this capability is needed.
metadata:
  author: kimhons
---

## Ralph Ultra Live Dashboard

Real-time terminal dashboard for monitoring the autonomous loop.

### What this shows

- Progress bar with current iteration / max
- Story status grid with icons (pass/fail/blocked/pending)
- Elapsed time and cost estimate
- Last skill results
- Live log tail (last 5 entries)

### Usage

```
/ralph-ultra:ralph-dashboard
```

### Controls

- `q` — Quit dashboard
- `r` — Force refresh
- Auto-refreshes every 2 seconds

### Requirements

Terminal with ANSI color support. Falls back gracefully on basic terminals.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kimhons) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
