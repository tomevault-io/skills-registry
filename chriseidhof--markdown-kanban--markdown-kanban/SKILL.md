---
name: markdown-kanban
description: Work with the Markdown Kanban app in this repo: read/edit task Markdown files in the board/ lane folders, move tasks between lanes, create tasks, and run the local web UI/server. Use when an agent needs to update kanban data stored as plain text or modify the app that manages it. Use when this capability is needed.
metadata:
  author: chriseidhof
---

# Markdown Kanban

## Overview

Use this skill to manage the file-based kanban board in this repo. Tasks are Markdown files inside lane folders, with the first line as the title and the rest as the body.

## Quick start

- Read tasks directly from `board/<Lane>/*.md`.
- Create a task by writing a new `.md` file with title on the first line; keep filenames as a title-based slug and de-duplicate with `-2`, `-3`, etc.
- Move a task by moving the file between lane folders.
- Run the UI with:
  - `npm install`
  - `npm run build`
  - `npm start`
  - Open `http://localhost:3000`

## Task format and storage

- Lanes are directories inside `board/` and may contain spaces.
- Task body can include Markdown, including manual image links.
- Read `references/board-format.md` for the canonical layout and API endpoints.

## Planning tasks

- When asked to plan a task, first move the task into `board/Planning` before doing anything else.
- Then search for it in the `board/Planning` lane to confirm the file and current content.
- If the task is not found, create it in `board/Planning` with a clear title and a short plan in the body.
- After planning, move the task to `board/Ready to run`.

## How to implement this task

Follow this change plan when asked to implement a task related to the board:

1. Move the task file into `board/In Progress` before making any changes.
2. Inspect the current lane folder that matches the task state (for planning work, start in `board/Planning`).
2. Identify the smallest set of files to change and explain the intent before editing.
3. Apply edits in `src/` for server behavior, `public/` for UI behavior, and `board/` for task data.
4. Keep task files simple: first line is the title, body is the plan or details; preserve existing Markdown.
5. Call out follow-up steps (build/run/verify) so the user can confirm the change.

## Editing rules

- Keep the first line as the task title.
- Preserve body text verbatim; allow blank lines.
- For images, store files in `board/assets` and link with `![](/assets/filename.png)`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/chriseidhof) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
