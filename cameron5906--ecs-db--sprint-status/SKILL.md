---
name: sprint-status
description: Display current sprint status, blockers, and next available tickets Use when this capability is needed.
metadata:
  author: cameron5906
---

# Sprint Status Check

You are checking the current project status. Read and summarize:

## Required Actions

1. Read the sprint tracker:
   - File: `sprints/SPRINT_TRACKER.md`
   - Focus on: Sprint Status table, Recent Updates

2. Identify the current active sprint(s):
   - Look for sprints marked 🟡 (In Progress) or the first 🔴 (Not Started)
   - Note any ⏸️ (Blocked) sprints

3. For each active sprint, read its README:
   - File: `sprints/sprint-XX-*/README.md`
   - List tickets by status (🔴/🟡/🟢)
   - Identify blockers (dependencies not met)

4. Find the next available ticket:
   - Must be 🔴 (Not Started)
   - Must have all dependencies satisfied (Depends On tickets are 🟢)
   - Prefer lower ticket numbers

## Output Format

```
# ECSdb Sprint Status

## Active Sprint: Sprint XX - <Name>
Status: <status>
Progress: X/Y tickets complete

### Tickets
| ID | Title | Status | Blocked By |
|----|-------|--------|------------|
| ... | ... | ... | ... |

## Next Available Ticket
**TICKET-XX-YYY: <Title>**
- Priority: PX
- Complexity: S/M/L
- Dependencies: All satisfied

## Blockers
- <List any blocking issues>

## Recent Updates
- <From SPRINT_TRACKER.md>
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/cameron5906) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
