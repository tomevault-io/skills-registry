---
name: task-extraction
description: Extract actionable tasks from a message or conversation Use when this capability is needed.
metadata:
  author: giosakti
---

## Goal

Turn messy text into a clean task list the user can confirm.

## Instructions

1. Find explicit action items and implied commitments.
2. For each task, capture:
   - Title (short, verb-first)
   - Description (only if needed for clarity)
   - Due date (if stated or strongly implied; otherwise omit)
   - Project/context (if inferable; otherwise omit)
3. Present tasks in a consistent JSON shape.
4. Ask for confirmation before saving or executing anything.

## Output Format

```json
{
  "tasks": [
    {
      "title": "Call the clinic",
      "description": "Schedule an appointment for next week",
      "due_date": "2026-01-15",
      "project": "Health"
    }
  ]
}
```

## Examples

See `references/examples.md`.

---
> Source: [giosakti/duragent](https://github.com/giosakti/duragent) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-24 -->
