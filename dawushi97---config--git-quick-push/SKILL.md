---
name: git-quick-push
description: Quickly complete git add, commit, and push workflow. Use when user wants to commit code, push changes, or says things like "commit this", "push the code", "ship it", etc. Use when this capability is needed.
metadata:
  author: dawushi97
---

## Context

- Current git status: !`git status`
- Current changes (staged + unstaged): !`git diff HEAD`
- Current branch: !`git branch --show-current`

## Task

Based on the above changes, execute on the current branch:

1. `git add .` to stage all changes
2. `git commit -m "<message>"` with an auto-generated message based on the diff
3. `git push` to push to remote

Requirements:
- Complete all operations in a single message
- Commit message should be concise and describe the core changes
- If push fails (e.g., remote has new commits), prompt user to pull first

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/dawushi97) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
