---
name: start-session
description: Start a new tutoring session. Reads progress, shows concepts due for review, suggests what to work on next. Use when user says "start session", "begin", "let's start", or at the beginning of a learning session. Use when this capability is needed.
metadata:
  author: tcole333
---

# Start Learning Session

## Instructions

1. Read `progress.json` to get current state
2. Read `session_log.md` to see where we left off
3. Check for concepts due for review (last_practiced > 3 days ago)
4. Show 1-2 quick review questions from `quiz_bank.md` if concepts are due
5. Suggest what to work on next based on `meta.current_module`
6. Update `meta.last_session` timestamp in progress.json

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tcole333) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
