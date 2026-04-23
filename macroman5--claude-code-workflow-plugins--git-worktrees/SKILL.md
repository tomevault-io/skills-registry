---
name: git-worktrees
description: Use Git worktrees to isolate tasks and keep diffs small and parallelizable Use when this capability is needed.
metadata:
  author: macroman5
---

# Git Worktrees

## Purpose
Create parallel worktrees for distinct tasks to keep changes isolated and reviews clean.

## When to Use
- Parallel task execution; spikes; conflicting changes

## Behavior
1. Pre-check: `git status --porcelain` must be clean.
2. Suggest names: `wt-TASK-<id>` or `wt-<short-topic>`.
3. Commands:
   - Create: `git worktree add ../<name> <base-branch>`
   - Switch: open the new dir; confirm branch
   - Remove (after merge): `git worktree remove ../<name>`
4. Cleanup checklist.

## Guardrails
- Never create/remove with dirty status.
- Echo exact commands; do not execute automatically.

## Integration
- `/lazy task-exec` (optional), Coder agent setup phase.

## Example Prompt
> Create a dedicated worktree for TASK-1.2 on top of `feature/auth`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/macroman5) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
