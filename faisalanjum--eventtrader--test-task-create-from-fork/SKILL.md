---
name: test-task-create-from-fork
description: Test if tasks created by forked skill are visible to main conversation Use when this capability is needed.
metadata:
  author: faisalanjum
---

# Test: Task Creation from Forked Skill

**Objective**: Create a task from this forked skill and verify it exists.

## Instructions

1. Create a task with:
   - Subject: "Task created by forked skill"
   - Description: "This task was created inside test-task-create-from-fork skill. If main conversation can see this, cross-creation works."
   - ActiveForm: "Creating task from fork"

2. Run TaskList to confirm it was created

3. Write results to output file

## Output

Write results to: `earnings-analysis/test-outputs/task-create-from-fork-result.txt`

Format:
```
TEST: task-create-from-fork
TIMESTAMP: {ISO timestamp}

TASK CREATED:
- ID: {task id}
- Subject: Task created by forked skill
- Status: pending

TASKLIST AFTER CREATION:
{full output}

CONCLUSION: Task successfully created from forked skill context
```

Return the task ID so main conversation can check visibility.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/faisalanjum) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
