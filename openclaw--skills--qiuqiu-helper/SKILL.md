---
name: qiuqiu-helper
description: This is a multi-purpose helper skill for Jesse, designed to automate common workspace tasks. Use when this capability is needed.
metadata:
  author: openclaw
---
# SKILL.md - Qiuqiu Helper

This is a multi-purpose helper skill for Jesse, designed to automate common workspace tasks.

## Tools

### workspace_summary
- Description: Generates a brief summary of recent changes and current status of the workspace.
- Usage: Just call it to get a pragmatic overview.

### quick_note
- Description: Appends a quick timestamped note to a specified file in the memory folder.
- Parameters:
  - content: The text to save.
  - file: (Optional) Target filename, defaults to today's date.

### clean_logs
- Description: Deletes log files older than a specified number of days to save space.
- Parameters:
  - days: (Optional) Retention period in days, defaults to 7.
  - path: (Optional) Directory to clean, defaults to current logs directory.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
