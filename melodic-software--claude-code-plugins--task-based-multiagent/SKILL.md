---
name: task-based-multiagent
description: Set up task-based multi-agent systems with shared task files. Use when setting up parallel agent execution, designing worktree isolation patterns, or implementing task file coordination. Use when this capability is needed.
metadata:
  author: melodic-software
---

# Task-Based Multi-Agent Skill

Guide creation of task-based multi-agent systems using shared task files and worktree isolation.

## When to Use

- Setting up parallel agent execution
- Managing multiple concurrent workflows
- Scaling beyond single-agent patterns
- Building task queue systems

## Core Concept

Agents share a task file that acts as a coordination mechanism:

```markdown
## To Do
- [ ] Task A
- [ ] Task B

## In Progress
- [🟡 abc123] Task C - being worked on

## Done
- [✅ def456] Task D - completed
```

## Task File Format

`tasks.md`:

```markdown
# Tasks

## Git Worktree {worktree-name}

## To Do
[] Pending task description                           # Available
[⏰] Blocked task (waits for above)                   # Blocked
[] Task with #opus tag                                # Model override
[] Task with #adw_plan_implement tag                  # Workflow override

## In Progress
[🟡, adw_12345] Task being processed                  # Claimed by agent

## Done
[✅ abc123, adw_12345] Completed task                 # Commit hash saved
[❌, adw_12345] Failed task // Error reason           # Error captured
```

## Status Markers

| Marker | Meaning | State |
| --- | --- | --- |
| `[]` | Pending | Available for pickup |
| `[⏰]` | Blocked | Waiting for previous |
| `[🟡, {id}]` | In Progress | Being processed |
| `[✅ {hash}, {id}]` | Complete | Finished successfully |
| `[❌, {id}]` | Failed | Error occurred |

## Tag System

Tags modify agent behavior:

| Tag | Effect |
| --- | --- |
| `#opus` | Use Opus model |
| `#sonnet` | Use Sonnet model |
| `#adw_plan_implement` | Complex workflow |
| `#adw_build` | Simple build workflow |

## Implementation Architecture

```text
┌─────────────────────────────────────────┐
│            CRON TRIGGER                  │
│  (polls tasks.md every N seconds)        │
└─────────────────┬───────────────────────┘
                  │
        ┌─────────┼─────────┐
        │         │         │
        v         v         v
   ┌────────┐ ┌────────┐ ┌────────┐
   │ Task A │ │ Task B │ │ Task C │
   │Worktree│ │Worktree│ │Worktree│
   │   1    │ │   2    │ │   3    │
   └────────┘ └────────┘ └────────┘
```

## Setup Workflow

### Step 1: Create Task File

```markdown
# tasks.md

## To Do
[] First task to complete
[] Second task to complete
[⏰] Blocked until first completes

## In Progress

## Done
```

### Step 2: Create Data Models

```python
from pydantic import BaseModel
from typing import Literal, Optional, List

class Task(BaseModel):
    description: str
    status: Literal["[]", "[⏰]", "[🟡]", "[✅]", "[❌]"]
    adw_id: Optional[str] = None
    commit_hash: Optional[str] = None
    tags: List[str] = []
    worktree_name: Optional[str] = None
```

### Step 3: Create Trigger Script

```python
# adw_trigger_cron_tasks.py
def main():
    while True:
        tasks = parse_tasks_file("tasks.md")
        pending = [t for t in tasks if t.status == "[]"]

        for task in pending:
            if not is_blocked(task):
                # Mark as in progress
                claim_task(task)
                # Spawn subprocess
                spawn_task_workflow(task)

        time.sleep(5)  # Poll interval
```

### Step 4: Create Task Workflows

```python
# adw_build_update_task.py (simple)
def main(task_id: str):
    # Mark in progress
    update_task_status(task_id, "[🟡]")

    # Execute /build
    response = execute_template("/build", task_description)

    # Mark complete
    if response.success:
        update_task_status(task_id, "[✅]", commit_hash)
    else:
        update_task_status(task_id, "[❌]", error_reason)
```

### Step 5: Add Worktree Isolation

Each task gets its own worktree:

```bash
git worktree add trees/{task_id} -b task-{task_id} origin/main
```

## Coordination Rules

1. **Claim before processing**: Update status to `[🟡]` immediately
2. **Respect blocking**: Don't process `[⏰]` tasks until dependencies complete
3. **Update on completion**: Always update status, even on failure
4. **Include context**: Save commit hash, error reason, ADW ID

## Key Memory References

- @git-worktree-patterns.md - Worktree isolation
- @composable-primitives.md - Workflow composition
- @zte-progression.md - Scaling to ZTE

## Output Format

```markdown
## Multi-Agent System Setup

**Task File:** tasks.md
**Trigger Interval:** 5 seconds
**Max Concurrent:** 5 agents

### Components
1. Task file format with status markers
2. Data models (Task, Status, Tags)
3. Cron trigger script
4. Task workflow scripts
5. Worktree isolation

### Workflow Routing
- Default: adw_build_update_task.py
- #adw_plan_implement: adw_plan_implement_update_task.py
- #opus: Use Opus model

### Status Flow
[] -> [🟡, id] -> [✅ hash, id]
                -> [❌, id] // error
```

## Anti-Patterns

- Polling too frequently (< 1 second)
- Not claiming before processing (race conditions)
- Ignoring blocked tasks
- Not capturing failure reasons
- Running in same directory (no isolation)

## Version History

- **v1.0.0** (2025-12-26): Initial release

---

## Last Updated

**Date:** 2025-12-26
**Model:** claude-opus-4-5-20251101

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/melodic-software) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
