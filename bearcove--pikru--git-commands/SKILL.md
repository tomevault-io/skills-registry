---
name: git-commands
description: Git command conventions for pikru. Use when running any git commands to avoid blocking on interactive pager. Use when this capability is needed.
metadata:
  author: bearcove
---

# Git Commands

Always use `--no-pager` BEFORE the git command to avoid blocking on interactive pager:

```bash
git --no-pager log -10
git --no-pager diff
git --no-pager show
```

The `--no-pager` flag must come **before** the subcommand (log, diff, show, etc.), not after.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/bearcove) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
