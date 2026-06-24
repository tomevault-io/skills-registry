---
name: status
description: Show current project progress from tasks/todo.md and what to work on next Use when this capability is needed.
metadata:
  author: sakebomb
---

Show the current project status — what's done, what's in progress, and what's next.

## Process

1. **Read `tasks/todo.md`** — Parse the plan and identify:
   - Completed items (checked boxes)
   - In-progress items
   - Next unchecked items
   - Any upcoming checkpoints
2. **Check git state** — Current branch, uncommitted changes, recent commits.
3. **Check `tasks/lessons.md`** — Any recent entries relevant to current work.

## Output Format

```
## Project Status

**Branch**: feat/current-branch
**Plan status**: X/Y steps complete

### Completed
- [list of done items]

### In Progress
- [current work]

### Up Next
- [next 2-3 items]

### Blockers
- [any blocked items or concerns]

### Recent Lessons
- [relevant entries from lessons.md, if any]
```

Keep it concise — this is a quick status check, not a deep analysis.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sakebomb) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
