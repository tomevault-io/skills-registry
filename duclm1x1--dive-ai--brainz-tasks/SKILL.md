---
name: tasks
description: Manage Todoist tasks using the `todoist` CLI. Add, list, and complete tasks from the command line. Use when this capability is needed.
metadata:
  author: duclm1x1
---

# Tasks Skill

Wraps Todoist / Microsoft To-Do APIs to add, list, and complete tasks. Requires `TODOIST_API_TOKEN` or `MSGRAPH_TOKEN` env var.

## Listing Tasks

Show all pending tasks:

```bash
todoist list
```

## Adding Tasks

Create a new task with an optional due date:

```bash
todoist add "Review PR #42" --due "2026-02-05"
```

## Completing Tasks

Mark a task as done:

```bash
todoist complete <task_id>
```

## Install

```bash
pip install todoist-api-python
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/duclm1x1) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
