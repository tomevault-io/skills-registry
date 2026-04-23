---
name: task-management
description: Task management for FashionUnited using TASKS.md for personal tasks, GitHub Projects for team roadmaps, and beads for development issues. Reference this when the user asks about tasks, wants to track commitments, or needs day planning around editorial deadlines and sales cycles. Use when this capability is needed.
metadata:
  author: fuww
---

# Task Management

Tasks are tracked across three systems optimized for different workflows:

1. **TASKS.md** - Personal tasks and commitments (quick capture, local file)
2. **GitHub Projects** - Team roadmap using Now-Next-Later columns
3. **beads** - Development issues and bugs (`bd` CLI)

## FashionUnited Context

FashionUnited operates across 30+ markets and 9 languages with key coordination patterns:

- **Editorial deadlines**: Content publishes in European morning (CET) for maximum reach
- **Sales cycles**: Monthly advertising deadlines, quarterly campaign planning
- **Multi-timezone**: Amsterdam HQ leads, coordinate with global markets
- **Weekly rhythm**: Monday planning, Thursday reviews, Friday async-only

## Local Tasks (TASKS.md)

**Always use `TASKS.md` in the current working directory.**

- If it exists, read/write to it
- If it doesn't exist, create it with the template below

## GitHub Projects Integration

For team-level work, use GitHub Projects with the **Now-Next-Later** roadmap structure:

| Column | Purpose | Typical items |
|--------|---------|---------------|
| **Now** | Currently in progress (this sprint/week) | Active features, urgent fixes |
| **Next** | Committed for next cycle | Prioritized backlog items |
| **Later** | Future consideration | Ideas, improvements, tech debt |

**Commands:**
- Check roadmap via `~~project tracker` (GitHub)
- Move items between columns as priorities shift
- Link issues to projects for tracking

## beads Integration

For development issues, use the `bd` CLI:

```bash
bd ready              # Find unblocked work
bd show <id>          # Full issue details
bd create "Title"     # New issue
bd close <id>         # Complete work
bd sync               # Sync with git
```

**Workflow:**
- Personal tasks in TASKS.md
- Development issues in beads
- Team roadmap in GitHub Projects

## Dashboard Setup (First Run)

A visual dashboard is available for managing tasks and memory. **On first interaction with tasks:**

1. Check if `dashboard.html` exists in the current working directory
2. If not, copy it from `${CLAUDE_PLUGIN_ROOT}/skills/task-management/assets/dashboard.html` to the current working directory
3. Inform the user: "I've added the dashboard. Run `/productivity:start` to set up the full system."

The task board:
- Reads and writes to the same `TASKS.md` file
- Auto-saves changes
- Watches for external changes (syncs when you edit via CLI)
- Supports drag-and-drop reordering of tasks and sections

## Format & Template

When creating a new TASKS.md, use this exact template (without example tasks):

```markdown
# Tasks

## Active

## Waiting On

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
- Summarize Active and Waiting On sections
- Highlight anything overdue or urgent

**When user says "add a task" / "remind me to":**
- Add to Active section with `- [ ] **Task**` format
- Include context if provided (who it's for, due date)

**When user says "done with X" / "finished X":**
- Find the task
- Change `[ ]` to `[x]`
- Add strikethrough: `~~task~~`
- Add completion date
- Move to Done section

**When user asks "what am I waiting on":**
- Read the Waiting On section
- Note how long each item has been waiting

## Conventions

- **Bold** the task title for scannability
- Include "for [person]" when it's a commitment to someone
- Include "due [date]" for deadlines
- Include "since [date]" for waiting items
- Sub-bullets for additional context
- Keep Done section for ~1 week, then clear old items

### FashionUnited-specific conventions

- Tag editorial tasks with market codes (e.g., `[NL]`, `[DE]`, `[UK]`, `[US]`)
- Note timezone for cross-market deadlines (CET is default)
- Link to GitHub issues when task relates to development: `(#123)`
- Mark sales tasks with client/advertiser name

## Extracting Tasks

When summarizing meetings or conversations, offer to add extracted tasks:
- Commitments the user made ("I'll send that over")
- Action items assigned to them
- Follow-ups mentioned

Ask before adding - don't auto-add without confirmation.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/fuww) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
