---
name: cursor-pr-review
description: Review PR or branch changes with Cursor CLI using a different model (gemini-3-pro). Use when this capability is needed.
metadata:
  author: prassanna-ravishankar
---

# Cursor PR Review

Second opinion on code changes via Cursor CLI.

## Setup

Confirm with user:
- **Model**: `gemini-3-pro` / `gpt-4o` / `claude-sonnet`
- **Scope**: `all` / `security` / `bugs` / `performance` / `style`
- **Target**: PR number or "this branch"

## Hands-off

Triggers: "AFK", "review and fix", "handle it".

1. Run Cursor
2. Fix all issues found
3. Report summary

## Hands-on

Default mode.

1. Show instruction to user for approval/edits
2. Run Cursor
3. Present findings
4. User selects issues to fix
5. Fix together

## Run Cursor

```bash
agent -p --model "<MODEL>" --output-format text \
  "Review <TARGET> for <SCOPE> issues. Report each with file:line, severity, description, and fix."
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/prassanna-ravishankar) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
