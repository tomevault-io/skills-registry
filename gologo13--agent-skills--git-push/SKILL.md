---
name: git-push
description: Push current branch to origin and sync with remote updates; keywords: git, rebase. Use only on explicit request; before execution, review diffs and list impact scope. Use when this capability is needed.
metadata:
  author: gologo13
---

# Git Push (sync with origin)

## Overview

Push current branch to origin and sync with remote updates.

## Steps

1. **Fetch and rebase onto latest main (optional but recommended)**
    - `git fetch origin`
    - `git rebase origin/main || git rebase --abort` (if not on main, rebase your feature branch onto latest main)
2. **Push current branch**
    - `git push -u origin HEAD`
3. **If push rejected due to remote updates**
    - Rebase and push: `git pull --rebase && git push`

## Notes

- Prefer `rebase` over `merge` for a linear history.
- If you need to force push after a rebase: you need to ask the user if they want to force push: `git push --force-with-lease`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/gologo13) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
