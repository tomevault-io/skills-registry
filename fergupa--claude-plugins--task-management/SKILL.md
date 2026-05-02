---
name: task-management
description: Task management using a shared TASKS.md file, optimized for finance operations and month-end close workflows. Use when the user asks about their tasks, wants to add/complete tasks, needs help tracking commitments, or is managing close activities. Integrates with the journal-entry-prep skill to extract journal entry tasks from close checklists and meeting notes. Use when this capability is needed.
metadata:
  author: fergupa
---

# Task Management

Tasks are tracked in a `TASKS.md` file that both you and the user can edit.

## File Location

**Always use `TASKS.md` in the current working directory.**

- If it exists, read/write to it
- If it doesn't exist, create it with the template below

## Format & Template

When creating a new TASKS.md, use this template:

```markdown
# Tasks

## Active
<!-- Current tasks requiring action -->

## Journal Entries Due
<!-- JE prep, review, and posting tasks - ties to journal-entry-prep skill -->

## Waiting On
<!-- Blocked items: approvals, info from others -->

## Recurring - Month-End
<!-- Standard close tasks that repeat each period -->

## Someday

## Done
```

Task format:
- `- [ ] **Task title** - context, for whom, due date`
- Sub-bullets for additional details
- Completed: `- [x] ~~Task~~ (date)`

## How to Interact

**When user asks "what's on my plate" / "my tasks":**
- Read TASKS.md
- Summarize Active, Journal Entries Due, and Waiting On sections
- Highlight anything overdue or urgent
- Flag journal entries approaching close deadline

**When user says "add a task" / "remind me to":**
- Add to Active section with `- [ ] **Task**` format
- If the task is a journal entry, add to Journal Entries Due section instead
- Include context if provided (who it's for, due date, period)

**When user says "done with X" / "finished X":**
- Find the task
- Change `[ ]` to `[x]`
- Add strikethrough: `~~task~~`
- Add completion date
- Move to Done section

**When user asks "what am I waiting on":**
- Read the Waiting On section
- Note how long each item has been waiting

**When user says "close checklist" or "month-end status":**
- Show all tasks in Journal Entries Due and Recurring - Month-End
- Summarize: how many done vs remaining
- List any blocked items from Waiting On that affect close

## Journal Entry Integration

When the user works on journal entries using the journal-entry-prep skill, offer to:

1. **Track the JE as a task** - Add to Journal Entries Due with entry type, amount, and period
2. **Track approvals** - If the entry exceeds a threshold (e.g., >$250K needs CFO approval), add a Waiting On item for the approval
3. **Mark complete when posted** - Move to Done with the posting date

Example task for a journal entry:
```
- [ ] **Prepaid rent amortization** - $20,833.33, Feb 2026, auto-reversal set - due 2/5
  - 12-month amortization of $250K prepaid, month 2 of 12
```

## Extracting Tasks from Meetings and Conversations

When summarizing meetings or conversations, offer to add extracted tasks:
- Commitments the user made ("I'll send that over")
- Action items assigned to them
- Journal entries that need to be booked
- Approvals needed from others (add to Waiting On)
- Follow-ups mentioned

Ask before adding - don't auto-add without confirmation.

## Conventions

- **Bold** the task title for scannability
- Include "for [person]" when it's a commitment to someone
- Include "due [date]" for deadlines
- Include "period [month/year]" for accounting period
- Include "since [date]" for waiting items
- Sub-bullets for additional context (amounts, account codes, approval status)
- Keep Done section for ~1 week, then clear old items

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/fergupa) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
