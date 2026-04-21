---
name: next-phase
description: Show all tasks in the next phase of work to be done Use when this capability is needed.
metadata:
  author: jerichobob
---

# Show Next Phase

Show all tasks in the next phase of work.

## Pre-parsed Data

!`${CLAUDE_PLUGIN_ROOT}/scripts/specs-parse.sh next-phase`

## Instructions

1. If the data above shows `NO_TASKS_REMAINING`, report that all specs are complete and suggest creating a new spec with `/sdd:spec`.

2. Otherwise, display all tasks in the current working phase using the pre-parsed data above. Count done vs total from the `[x]` and `[ ]` lines in the TASKS section.

3. Display:
   - The spec version and name
   - The phase name
   - All tasks with their status (✅ or ⬜)
   - A summary of progress (e.g., "2/5 complete")

## Output Format

```text
📋 Next Phase

Spec: v{N} - {Name}
Phase: {Phase Name}
Progress: {done}/{total} complete

Tasks:
✅ {Completed task 1}
⬜ {Pending task 2}
⬜ {Pending task 3}
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jerichobob) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
