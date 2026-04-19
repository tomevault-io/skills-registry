---
name: implement
description: Implement tasks from the Markdown Kanban board when asked to "implement" work. Use when Codex should pull a task from `board/Ready to run`, move it to `board/In Progress`, make the code changes, update the task markdown with a rich-text change log, and then move it to `board/Done` or request input with a status line. Use when this capability is needed.
metadata:
  author: chriseidhof
---

# Implement

## Overview

Use this skill to execute a task from the kanban board end-to-end: move it into progress, implement the requested changes, document the result in the task file, and complete or flag the task.

## Workflow

### 1) Find the task in Ready to run

- List tasks in `board/Ready to run` and pick the task that matches the request.
- If multiple tasks match or the request is ambiguous, ask for clarification before moving anything.

### 2) Move to In Progress

- Move the task file from `board/Ready to run/` to `board/In Progress/`.
- Preserve the filename; do not alter the task title or body yet.

### 3) Implement the task

- Make the smallest set of code changes needed to satisfy the task.
- Follow repo conventions and keep edits focused.
- If the task requires updates to task data, do those edits in `board/`.

### 4) Update the task markdown with rich text

- Append a "Changes" section to the task body using Markdown (headings, bullets, links).
- Include a short narrative of what was implemented and key file references.
- Keep the first line as the task title; only modify the body.

Example:

```
## Changes
- Updated `src/server.ts` to validate inputs before save.
- Added `public/styles.css` rule for new task badge.
```

### 5) Complete or request input

- If the task is complete, move the task file to `board/Done/`.
- If you need user input, add a plain markdown status line near the top of the body (after the title) and keep the task in `board/In Progress/`.
  - Format: `Status: Needs input - <question or missing detail>`
  - The GUI will mark these tasks in red; keep the line concise and unambiguous.
  - Ask the user for the missing information in your response.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/chriseidhof) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
