---
name: changelog-observer
description: Track development session events in a daily markdown changelog, including file changes, test results, and key decisions. Use when this capability is needed.
metadata:
  author: claude-world
---

# Changelog Observer

Track session context in `.changelog/session-YYYY-MM-DD.md`.

## Log target

- Use `.changelog/session-YYYY-MM-DD.md` for the current local date.
- Create `.changelog/` and the session file if they do not exist.
- If the session file is new, initialize it from [template.md](template.md).
- Append new entries in chronological order.

## What to log

- `file-change`: after creating, editing, or deleting project files.
- `test-result`: after running tests, checks, or validation commands.
- `decision`: after making an implementation or architecture decision worth preserving.

## Entry rules

- Keep each description short and specific.
- List only directly affected files.
- Use `none` when no files were changed.
- Do not rewrite earlier entries unless they are incorrect.

## Template

Use the markdown structure in [template.md](template.md) for each appended entry.

---
> Source: [claude-world/director-mode-lite](https://github.com/claude-world/director-mode-lite) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-21 -->
