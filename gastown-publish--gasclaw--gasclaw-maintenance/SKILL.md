---
name: gasclaw-maintenance
description: New interval in seconds (for frequency action) Use when this capability is needed.
metadata:
  author: gastown-publish
---

# Gasclaw Maintenance Control

Control the Claude Code maintainer agent that autonomously maintains the gasclaw repo.

## Usage

```bash
bash ~/.openclaw/skills/gasclaw-maintenance/scripts/maintenance.sh status
bash ~/.openclaw/skills/gasclaw-maintenance/scripts/maintenance.sh trigger    # run now
bash ~/.openclaw/skills/gasclaw-maintenance/scripts/maintenance.sh pause
bash ~/.openclaw/skills/gasclaw-maintenance/scripts/maintenance.sh resume
bash ~/.openclaw/skills/gasclaw-maintenance/scripts/maintenance.sh frequency 600
bash ~/.openclaw/skills/gasclaw-maintenance/scripts/maintenance.sh restart
```

## State Files

- `/workspace/state/maintenance.json` — last run info, cycle count
- `/workspace/state/paused` — if present, maintenance is paused
- `/workspace/state/trigger-now` — write to trigger immediate run
- `/workspace/state/claude.pid` — Claude Code process PID

---
> Source: [gastown-publish/gasclaw](https://github.com/gastown-publish/gasclaw) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
