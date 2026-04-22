---
name: close
description: End the current session gracefully, updating all state files and preparing handoff for next session Use when this capability is needed.
metadata:
  author: kheery12
---

# Close Session Skill

## Purpose
Gracefully end the current working session by:
1. Summarizing accomplishments
2. Updating Context.md with any changes
3. Writing session-handoff.md for continuity
4. Calculating final House Cup points
5. Archiving session to Pensieve

## Execution Steps

### Step 1: Gather Session Data
- Count tasks completed per house
- Calculate total points earned this session
- Identify any incomplete work
- Note any blockers or decisions needed

### Step 2: Update Context.md
If any of these changed during the session, update Context.md:
- Tech stack (if configured)
- Project structure (if explored)
- Current goals (if completed or changed)
- Known issues (if discovered)

### Step 3: Write Session Handoff
Update `logs/session-handoff.md` with:
- Session date and duration
- List of accomplishments (checkmarks)
- Where work stopped (file, line, task)
- Blockers requiring human decision
- Recommended first action for next session
- Any paused students and their status

### Step 4: Session House Cup Ceremony
Display:
```
SESSION CLOSING CEREMONY

SESSION RESULTS
| House        | Points | Tasks      |
|--------------|--------|------------|
| Ravenclaw    | +XX    | X planned  |
| Gryffindor   | +XX    | X built    |
| Slytherin    | +XX    | X tested   |
| Hufflepuff   | +XX    | X helped   |

HANDOFF COMPLETE
-> [First recommended action]
-> [Second if applicable]

The castle rests. Until next time, Headmaster.
```

### Step 5: Archive to Pensieve (Optional)
If significant work was done, create:
`logs/pensieve/[YYYY-MM-DD]-session.md`

With full session summary for historical reference.

## Output Format
Always end with the ceremony display above.
Make the human feel closure and confidence in continuity.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kheery12) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
