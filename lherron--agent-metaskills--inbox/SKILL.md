---
name: inbox
description: Review inbox tasks and select highest priority work. Use when asked to check inbox, find next task, or start working on pending items. Use when this capability is needed.
metadata:
  author: lherron
---

# Inbox Task Selector

Select the highest priority task from the inbox and shape the implementation approach with the user.

## Instructions

### Step 1: Fetch Inbox Tasks

Run the check-inbox command to get open tasks:

```bash
wrkq check-inbox --json
```

### Step 2: Select Highest Priority Task

From the JSON output, identify the task with:
1. **Lowest priority number** (1 = highest priority, 4 = lowest)
2. Among equal priorities, prefer tasks with earlier `created_at`

### Step 3: Display Selected Task

Show the user:
- Task ID and title
- Priority level
- Current state
- Brief summary of what the task involves

Read the full task details:

```bash
wrkq cat <task-id>
```

### Step 4: Understand the Ask

Before proposing implementation:
1. Read the task description thoroughly
2. Explore any referenced files or code
3. Understand the scope and constraints

### Step 5: Shape the Implementation

After understanding the task, use the **prompt-shaping** skill to collaborate with the user on the implementation approach:

1. Propose a structured interpretation of the task
2. Break down into actionable steps
3. Identify any ambiguities or decisions needed
4. Get user alignment before proceeding

To invoke prompt-shaping, use the Skill tool:
```
Skill: prompt-shaping
```

### Step 6: Begin Work

Once the user approves the shaped approach:
1. Set the task to `in_progress`
2. Create a TodoWrite list for tracking
3. Begin implementation

```bash
wrkq set <task-id> --state in_progress
```

## Example Flow

```
User: /inbox

Claude: Let me check your inbox for the highest priority task...

[Runs: wrkq check-inbox --json]

Found 3 open tasks. Selecting highest priority:

**T-00036: Implement deleted state and restore command**
- Priority: 3
- State: open
- Created: 2025-12-28

Let me read the full task details...

[Runs: wrkq cat T-00036]

This task involves adding a "deleted" lifecycle state and a restore command.

Now let me shape the implementation with you...

[Invokes prompt-shaping skill]
```

## Notes

- Always show the user which task was selected and why
- If multiple tasks have equal priority, briefly mention alternatives
- Use prompt-shaping to ensure alignment before coding
- Track progress with wrkq comments as work proceeds

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/lherron) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
