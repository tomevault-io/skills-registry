---
name: readonly-mode
description: **`GOAL`**: Activate read-only mode by creating the `.gemini_readonly` Use when this capability is needed.
metadata:
  author: mystilleef
---

# Read-only mode

**`GOAL`**: Activate read-only mode by creating the `.gemini_readonly`
marker file.

**`WHEN`**: Invoke this skill when you need to ensure no further write
operations occur in the current session or when explicitly requested by
the user.

**`NOTE`**: This skill creates a file that whitelisted hooks use to
block write-capable tools.

## Efficiency directives

- Optimize all operations for agent, token, and context efficiency
- Target only relevant files
- Don't use your task management system for this skill
- Don't perform a vibe check for this skill

## Workflow

- Execute `scripts/enable-readonly-mode.sh`
- **`DONE`**

## Output

**Files created/modified:**

- `.gemini_readonly` - Marker file to enable read-only mode.

**Status communication:** Report status of script operation.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mystilleef) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
