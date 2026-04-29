---
name: start
description: Start work on a ClickUp task (transition to In Progress) Use when this capability is needed.
metadata:
  author: thebushidocollective
---

# start

## Name

clickup:start - Start work on a ClickUp task (transition to In Progress)

## Synopsis

```
/start [arguments]
```

## Description

Start work on a ClickUp task (transition to In Progress)

## Implementation

Start work on a ClickUp task by transitioning it to "In Progress" and displaying checklist.

**Usage**: `/start #ABC123` or `/start ABC123`

**Steps**:

1. Use `clickup_get_task` to fetch task details
2. Display task name and current status
3. Show checklist items if present
4. Use `clickup_update_task_status` to transition to "in progress"
5. Use `clickup_add_comment` to add: "Starting work on this task"
6. Optionally use `clickup_assign_task` to assign to current user if unassigned

**Display Format**:

```
▶️  Starting work on #ABC123

Name: {task name}
Status: {old status} → In Progress
Assignees: {assignees}

📋 Checklist:
- [ ] {checklist item 1}
- [ ] {checklist item 2}
- [ ] {checklist item 3}

Link: {task URL}
```

If task is already In Progress, just display current status and checklist without transitioning.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/thebushidocollective) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
