---
name: planning-with-files
description: | Use when this capability is needed.
metadata:
  author: yilabsai
---

# Planning with Files

A task planning skill that helps you manage complex tasks through structured plan files.

## Overview

This skill creates and maintains a `PLAN.md` file that tracks:
- Task breakdown into subtasks
- Progress status for each subtask
- Completion criteria

## Usage

When starting a complex task, create a plan:

```
/plan create "Implement user authentication"
```

The skill will:
1. Create a `PLAN.md` file with task breakdown
2. Track progress as you work through subtasks
3. Prevent premature completion until all subtasks are done

Common action examples:

```bash
# Create a plan
/plan create "Implement user authentication"

# Update subtask #0 to completed=true
/plan update 0 true

# Check plan status
/plan status

# Mark plan complete (only when all subtasks are done)
/plan complete
```

## Plan File Format

```markdown
# Task: [Task Description]

## Status: in_progress | completed | blocked

## Subtasks

- [ ] Subtask 1 description
- [x] Subtask 2 description (completed)
- [ ] Subtask 3 description

## Notes

Any relevant notes or context.
```

## Hooks

### PreToolUse (Write/Edit)

Before any file write, checks if the operation aligns with the current plan.

### PostToolUse

After each tool use, updates plan progress if relevant.

### Stop

Before stopping, verifies all planned subtasks are complete.
If incomplete tasks remain, prompts to continue or explicitly acknowledge incomplete work.

## Input Schema

```json
{
  "type": "object",
  "properties": {
    "action": {
      "type": "string",
      "enum": ["create", "update", "complete", "status"],
      "default": "create",
      "description": "Action to perform on the plan"
    },
    "task": {
      "type": "string",
      "description": "Task description (for create action)"
    },
    "subtasks": {
      "type": "array",
      "items": { "type": "string" },
      "description": "Optional subtask list (for create action)"
    },
    "subtask_index": {
      "type": "integer",
      "minimum": 0,
      "description": "Subtask index to update (for update action)"
    },
    "completed": {
      "type": "boolean",
      "default": true,
      "description": "Whether the target subtask is completed (for update action)"
    }
  },
  "required": ["action"]
}
```

## Output Schema

```json
{
  "type": "object",
  "properties": {
    "success": {
      "type": "boolean"
    },
    "plan_path": {
      "type": "string",
      "description": "Path to the plan file"
    },
    "status": {
      "type": "string",
      "description": "Current plan status"
    },
    "progress": {
      "type": "object",
      "properties": {
        "total": { "type": "integer" },
        "completed": { "type": "integer" },
        "percentage": { "type": "number" }
      }
    },
    "message": {
      "type": "string"
    }
  }
}
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/yilabsai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
