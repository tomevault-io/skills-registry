---
name: git-sync
description: Sync with remote repository using pull --rebase. Use when you need to update your local branch with remote changes. Use when this capability is needed.
metadata:
  author: toilahuongg
---

# Git Sync

Sync local branch with remote using rebase.

1. Fetch the latest changes from the remote repository.
   - Command: `git fetch origin`

2. Pull and rebase the current branch on top of the remote branch.
   - Command: `git pull origin HEAD --rebase`
   - If there are conflicts, resolve them, then `git rebase --continue`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/toilahuongg) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-12 -->
