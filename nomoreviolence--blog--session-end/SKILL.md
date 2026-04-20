---
name: session-end
description: End the current development session with a summary. Use when user wants to finish tracking their work. Use when this capability is needed.
metadata:
  author: nomoreviolence
---

# End the current development session

## Steps

1. Read `.claude/sessions/.current-session` to find active session filename
2. If no active session, inform user and suggest `/session-start`

## Update the session file

1. Add final summary to Progress section with timestamp
2. Update status from "In Progress" to "Completed"
3. Add end time to Overview section
4. Summarize key accomplishments
5. Note any unfinished work or follow-ups needed

## Cleanup

Delete `.claude/sessions/.current-session` file to mark session as ended.

## Confirmation

Show user:

- Session duration
- Summary of completed goals
- Any remaining todos for next session

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/nomoreviolence) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
