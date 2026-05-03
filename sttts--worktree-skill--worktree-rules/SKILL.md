---
name: git-worktree-rules
description: This skill should be used when the user mentions "worktree", "worktrees", "create worktree", or asks about working in a separate branch directory. Use when this capability is needed.
metadata:
  author: sttts
---

# Git Worktree Rules

These rules apply whenever working with git worktrees.

## Directory Location

- MUST create worktrees inside `.git/checkouts/` directory.

## Critical Restrictions

- MUST NEVER leave a worktree once working in it. Do all work there.
- MUST NEVER touch main when working in a worktree. This is critical.
- MUST NEVER delete a worktree before confirming its content is merged.
- MUST NEVER mark a task as completed until the branch is merged (merging happens outside the worktree).

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sttts) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
