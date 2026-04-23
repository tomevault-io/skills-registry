---
name: dev-docs-workflow
description: Dev docs system for maintaining context across sessions Use when this capability is needed.
metadata:
  author: imehr
---

# Dev Docs Workflow

## Overview

This skill guides you through the dev docs system for maintaining context across Claude Code sessions. The dev docs system solves the problem of context loss during long-running tasks by creating persistent documentation files.

## Quick Reference

| Command | Purpose | When to Use |
|---------|---------|-------------|
| `/dev-docs [task]` | Create new dev docs | Starting a new feature/task |
| `/dev-docs-update [task]` | Update existing docs | Before context reset or end of session |

## The Three-File System

Every task in `dev/active/` has three documentation files:

```
dev/active/[task-name]/
├── [task-name]-plan.md      # The strategic implementation plan
├── [task-name]-context.md   # Key files, decisions, gotchas
└── [task-name]-tasks.md     # Checklist of work items
```

### File Purposes

| File | Contains | Update Frequency |
|------|----------|-----------------|
| **plan.md** | High-level strategy, phases, architecture decisions | Rarely (only if approach changes) |
| **context.md** | Key files discovered, decisions made, gotchas found | Often (as you learn things) |
| **tasks.md** | Checklist items, progress tracking, estimates | Every session |

## Workflow

### Starting a New Task

1. **Use planning mode first** (or `strategic-plan-architect` agent)
2. **Review and approve the plan**
3. **Run `/dev-docs [task-name]`** to create the three files
4. **Begin implementation** from the tasks checklist

### During Implementation

1. **Work in small increments** (1-2 tasks at a time)
2. **Update context.md** when you discover important information
3. **Mark tasks complete** in tasks.md as you finish them
4. **Request code review** after completing major sections

### Before Ending a Session

**CRITICAL**: Run `/dev-docs-update [task-name]` before:
- Context reset/compaction
- Ending for the day
- Switching to a different task

### Resuming Work

1. **Read all three files** in `dev/active/[task-name]/`
2. **Check tasks.md** for what's next
3. **Review context.md** for important context
4. **Continue from where you left off**

## Plan File Structure

```markdown
# [Task Name] - Implementation Plan

## Executive Summary
[1-2 paragraph overview]

## Current State Analysis
[What exists now, what needs to change]

## Proposed Solution
[Architecture, approach, key decisions]

## Implementation Phases
### Phase 1: [Name]
- Task 1.1
- Task 1.2
### Phase 2: [Name]
...

## Risk Assessment
| Risk | Mitigation |

## Success Metrics
- [ ] Metric 1
- [ ] Metric 2
```

## Context File Structure

```markdown
# [Task Name] - Context

## Key Files
| File | Purpose | Notes |
|------|---------|-------|

## Decisions Made
| Decision | Rationale | Date |
|----------|-----------|------|

## Gotchas & Lessons Learned
- Gotcha 1
- Gotcha 2

## Next Steps
1. Next step 1
2. Next step 2

---
Last Updated: [timestamp]
```

## Tasks File Structure

```markdown
# [Task Name] - Tasks

## Progress: X/Y complete (Z%)

## Phase 1: [Name]
- [x] Completed task
- [ ] Pending task
- [ ] Another pending task

## Phase 2: [Name]
- [ ] Future task

---
Last Updated: [timestamp]
Last Action: [what was just done]
Next Action: [what to do next]
```

## Best Practices

### DO

- Update context.md when you learn something important
- Mark tasks complete immediately after finishing
- Keep the "Last Updated" timestamp current
- Document decisions and their rationale
- Note file paths that are important

### DON'T

- Wait until the end to update docs
- Skip the context file updates
- Leave tasks unmarked after completing them
- Forget to run `/dev-docs-update` before session end

## Integration with Other Skills

- **planning**: Use before creating dev docs
- **code-review-workflow**: Use between implementation phases
- **git-workflow**: Commit after completing each phase

## Resources

| Topic | Link |
|-------|------|
| Plan Template | [mdc:resources/plan-template.md] |
| Context Template | [mdc:resources/context-template.md] |
| Tasks Template | [mdc:resources/tasks-template.md] |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/imehr) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
