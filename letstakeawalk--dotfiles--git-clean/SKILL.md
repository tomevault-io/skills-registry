---
name: git-clean
description: Clean up local branches marked as [gone] and their associated worktrees Use when this capability is needed.
metadata:
  author: letstakeawalk
---

## Context
- Branches: !`git branch -v`
- Worktrees: !`git worktree list`

## Task

Clean up local branches that have been deleted from the remote (marked `[gone]`).

### Steps

1. From the branch list above, identify branches marked `[gone]`
2. If none found, report that no cleanup is needed and stop
3. **List the branches that will be deleted and ask for confirmation before proceeding**
4. For each confirmed branch:
   - If it has an associated worktree (shown in worktree list), remove the worktree first with `git worktree remove --force`
   - Delete the branch with `git branch -D`
5. Report what was removed

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/letstakeawalk) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
