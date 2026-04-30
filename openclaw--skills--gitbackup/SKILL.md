---
name: gitbackup
description: Create a local Git bundle backup of the OpenClaw workspace repository. Use when running /gitbackup in Telegram or when the user asks to back up Git history/refs to a local file. Use when this capability is needed.
metadata:
  author: openclaw
---

# Git Backup (local bundle)

## Overview
Create a self-contained Git bundle of the workspace repo and store it in `/root/.openclaw/backups`.

## Quick start
Run the bundled script:
```bash
bash /root/.openclaw/workspace/skills/gitbackup/scripts/git-backup.sh
```

## Output
Print the bundle path and size. The bundle filename includes UTC timestamp.

## Notes
- If the workspace is not a Git repo, exit with a clear error.
- Do not delete older bundles.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
