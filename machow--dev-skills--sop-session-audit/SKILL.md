---
name: sop-session-audit
description: | Use when this capability is needed.
metadata:
  author: machow
---

# Session Audit SOP

## Goal

Capture what matters from a session:
- **What we did** - changes made, files modified
- **Key decisions** - choices and their rationale
- **Important feedback** - user corrections that shaped the work
- **Patterns to remember** - lessons for future sessions

## Procedure

1. **Name the file with date**: `.claude/audits/YYYY-MM-DD-feature-name.md`
   ```bash
   /bin/date +%Y-%m-%d  # get current date
   ```

2. **Write audit following template**: See [TEMPLATE.md](TEMPLATE.md)

3. **Ensure feedback captures conversation flow** - not just the end result, but the progression of corrections

4. **If no feedback recorded, think carefully** - most sessions have at least some course corrections worth noting

5. **Summarize to user after writing**: Report back with:
   - Audit file name
   - Key feedback points (quote the "Important Feedback" section)
   - Patterns learned (list from "Patterns to Remember")

   This ensures the user sees what was captured without having to open the file.

**Note**: Audit files are gitignored - don't commit them.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/machow) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
