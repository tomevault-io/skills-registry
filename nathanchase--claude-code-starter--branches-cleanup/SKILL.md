---
name: branches-cleanup
description: Automatically clean up local git branches that have been merged into the base branch Use when this capability is needed.
metadata:
  author: nathanchase
---

# Cleanup Merged Branches

Automatically clean up local git branches that have been merged into the base branch.

## Instructions

When this skill is invoked manually, clean up merged branches:

1. Run `git branch --merged <base-branch>` to find branches merged into the base branch
2. Exclude protected branches: `develop`, `main`, `master`
3. For each merged branch found:
   - Check if it has an associated worktree with `git worktree list`
   - If worktree exists, remove it with `git worktree remove <path>`
   - Delete the local branch with `git branch -d <branch>`
4. Report what was cleaned up

## Protected Branches (NEVER delete)
- `develop`
- `main`
- `master`

## Automation

This skill automatically runs after `git pull` via the PostToolUse hook in `.claude/settings.json`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/nathanchase) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
