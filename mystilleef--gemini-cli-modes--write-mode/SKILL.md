---
name: write-mode
description: **`GOAL`**: Activate write mode by removing the `.gemini_readonly` Use when this capability is needed.
metadata:
  author: mystilleef
---

# Write mode

**`GOAL`**: Activate write mode by removing the `.gemini_readonly`
marker file.

**`WHEN`**: Invoke this skill ONLY when the user explicitly authorizes
write access or requests to disable read-only mode.

**`NOTE`**: This skill removes the file that whitelisted hooks use to
block write-capable tools.

## Efficiency directives

- Optimize all operations for agent, token, and context efficiency
- Target only relevant files
- Don't use your task management system for this skill
- Don't perform a vibe check for this skill

## Workflow

- Execute `scripts/enable-write-mode.sh`
- **`DONE`**

## Output

**Files created/modified:**

- `.gemini_readonly` - Marker file removed to enable write mode.

**Status communication:** Report status of script operation.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mystilleef) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
