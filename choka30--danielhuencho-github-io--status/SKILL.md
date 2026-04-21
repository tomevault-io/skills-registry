---
name: status
description: Show comprehensive project status overview including git state, content progress, build health, and brain sync status. Read-only operation. Use anytime to understand current state. Use when this capability is needed.
metadata:
  author: choka30
---

# Status

Display comprehensive project status overview.

## Execution Protocol

### Step 1: Gather Git Status

```bash
BRANCH=$(git branch --show-current 2>/dev/null || echo "not a git repo")
UNCOMMITTED=$(git status --porcelain 2>/dev/null | wc -l)
LAST_COMMIT=$(git log -1 --oneline 2>/dev/null || echo "no commits")
```

### Step 2: Gather Plan Status

Read `brain/plan.md` and calculate:
- Total tasks in session
- Completed tasks
- In-progress tasks
- Pending tasks

### Step 3: Gather Content Status

Read `brain/codebase_index.md` and summarize:
- Pages by status (Complete, Placeholder, Empty, Review needed)
- Assets inventory (Missing, Present)

### Step 4: Check Build Health

```bash
# Quick render check
quarto render 2>&1 | tail -3
```

## Output Format

```
═══════════════════════════════════════════════════════
  📊 PROJECT STATUS
═══════════════════════════════════════════════════════

## Git
  Branch:        content/about-page
  Uncommitted:   2 files
  Last commit:   abc1234 content(index): add hero section

## Session Progress
  ████████░░░░░░░░░░░░  40% (2/5 tasks)

  ✓ Task 1: Create about.qmd with front matter
  ✓ Task 2: Add Education section
  → Task 3: Add Professional Experience section (in progress)
  ○ Task 4: Add Technical Skills section
  ○ Task 5: Add Research Interests section

## Content Status
  ┌─────────────────────┬──────────────┐
  │ Page                │ Status       │
  ├─────────────────────┼──────────────┤
  │ index.qmd           │ ✓ Complete   │
  │ about.qmd           │ → In Progress│
  │ dashboard.qmd       │ ⚠ Review     │
  └─────────────────────┴──────────────┘

## Assets
  │ profile.jpg         │ ✗ Missing    │
  │ cv.pdf              │ ✗ Missing    │

## Build Health
  Status: ✓ No errors

## Brain Sync
  │ plan.md             │ ✓ Synced     │
  │ codebase_index.md   │ ⚠ Stale      │

═══════════════════════════════════════════════════════

## Next Action
  Continue with `/task "Add Professional Experience section"`
```

## Status Indicators

| Indicator | Meaning |
|-----------|---------|
| ✓ | Complete / Synced |
| → | In progress |
| ○ | Pending |
| ⚠ | Needs attention |
| ✗ | Missing / Error |

## Quick Status (Short Form)

```
Status: content/about | 2/5 tasks | Build ✓ | Brain ⚠
```

## When to Check Status

- Start of session
- Before committing
- When returning after a break
- When unsure what to do next

## Notes

- This is a **read-only** operation
- No files are modified
- Can be run anytime without side effects

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/choka30) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
