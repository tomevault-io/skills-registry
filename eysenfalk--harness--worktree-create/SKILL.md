---
name: worktree-create
description: Create an isolated git worktree for parallel agent work. Use when this capability is needed.
metadata:
  author: eysenfalk
---

# /worktree-create

Create worktree for task: $ARGUMENTS

```bash
# Check max concurrent worktrees (5 limit)
git worktree list | wc -l

# Create branch and worktree
git worktree add ../worktree-$ARGUMENTS -b task/$ARGUMENTS

# Verify
git worktree list
```

Report the worktree path: `../worktree-$ARGUMENTS`

Rules:

- Max 5 concurrent worktrees
- If worktree already exists for this task, report it
- Branch naming: `task/<task-id>`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/eysenfalk) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
