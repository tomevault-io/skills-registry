---
name: daily-briefing
description: | Use when this capability is needed.
metadata:
  author: gaodes
---

# Daily Briefing Skill

Generate comprehensive daily briefing with priorities, progress, and alerts.

## When to Use

- Part of `marvin` skill (session start)
- User asks "what's on today" or "daily briefing"
- Morning check-in requests

## Process

### Step 1: Calendar Overview (if available)
- Today's events with times
- Tomorrow's events (preview)
- Next 7 days: any important deadlines

### Step 2: Task Status
From `state/current.md`:
- Active priorities
- Overdue items
- Due today
- Open threads needing attention

### Step 3: Progress Check
For current month from `state/goals.md`:
- Progress against each goal
- Days remaining in month

If behind pace, flag it.

### Step 4: Open Threads
From `state/current.md`:
- Anything waiting on follow-up
- Stale threads (no update > 5 days)

### Step 5: Proactive Suggestions
Based on patterns:
- "You haven't made progress on {goal} this week"
- "Deadline for {item} is in 3 days"
- "Monthly review coming up — want to schedule?"

## Output Format

Keep concise. Structure as:
```
## {Day}, {Date}

**Today**: {summary}

**Alerts**:
- {any urgent items}

**Progress**: {goal status summary}

**Focus**: {top 1-2 priorities}
```

Offer to expand any section on request.

---

*Skill created: 2026-01-22*

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/gaodes) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
