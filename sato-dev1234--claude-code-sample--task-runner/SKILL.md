---
name: task-runner
description: Executes pending autoRun tasks based on TaskList conditions Use when this capability is needed.
metadata:
  author: sato-dev1234
---

# /task-runner

Automatic task execution controller.

## Progress Checklist

```
- [ ] Step 1: Get all tasks
- [ ] Step 2: Find executable tasks
- [ ] Step 3: Execute or exit
```

## Steps

1. Get all tasks:
   - Call TaskList tool

2. Find executable tasks:
   - From TaskList result, find tasks meeting ALL conditions:
     - status: pending
     - metadata.autoRun: true
     - blockedBy is empty OR all blockedBy tasks are completed

3. Execute or exit:
   - **If no executable tasks found:** END (no report needed)
   - **If executable tasks found:**
     - For each task:
       1. TaskUpdate: set status to "in_progress"
       2. TaskGet to read full task details including metadata
       3. If metadata.skill exists: Call Skill tool with skill=metadata.skill, args=metadata.args
       4. After skill completes: TaskUpdate: set status to "completed"
     - **Parallel execution:** If multiple tasks meet conditions, call multiple Skill tools in parallel

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sato-dev1234) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
