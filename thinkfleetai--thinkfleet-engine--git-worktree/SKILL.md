---
name: git-worktree
description: Manage git worktrees for parallel development — create, list, and remove isolated working directories. Use when this capability is needed.
metadata:
  author: thinkfleetai
---

# Git Worktree

Manage git worktrees for parallel, isolated development.

## Create a worktree

```bash
git worktree add .worktrees/IDENTIFIER -b feature/IDENTIFIER main
```

Creates a new worktree at `.worktrees/IDENTIFIER` on branch `feature/IDENTIFIER` based off `main`.

## List worktrees

```bash
git worktree list
```

Shows all active worktrees with their paths, HEAD commits, and branch names.

## Remove a worktree

```bash
git worktree remove .worktrees/IDENTIFIER --force
git branch -D feature/IDENTIFIER
```

Removes the worktree directory and deletes the associated branch.

## Prune stale worktrees

```bash
git worktree prune
```

Cleans up stale worktree references (e.g., after manually deleting a directory).

## Notes

- Worktrees allow multiple branches to be checked out simultaneously in separate directories.
- Each worktree has its own working directory but shares the same `.git` repository.
- Always confirm before removing worktrees that may contain uncommitted changes.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/thinkfleetai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
