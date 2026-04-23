---
name: commit-and-push
description: Create a git commit and push Use when this capability is needed.
metadata:
  author: dparedesi
---

## Context

- Current git status: !`git status`
- Current git diff (staged and unstaged changes): !`git diff HEAD`
- Current branch: !`git branch --show-current`
- Recent commits: !`git log --oneline -10`

## Your task

1. Analyze the diff in depth - understand what changed and why
2. Create a single commit with a clear, descriptive message
3. Push to origin
4. If on a feature branch: create a PR using `gh pr create`
5. If on main: just push (no PR needed)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/dparedesi) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
