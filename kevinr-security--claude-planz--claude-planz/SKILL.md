---
name: claude-planz
description: Background knowledge for claude-planz workflows. Provides Beads CLI conventions and file structure guidance. Use when this capability is needed.
metadata:
  author: kevinr-security
---

# claude-planz Conventions

claude-planz is a lightweight research and planning extension that integrates with Beads CLI for task tracking.

## Beads CLI Quick Reference

```bash
bd init                          # Initialize Beads in current project
bd ready                         # List tasks with no open blockers
bd show <id>                     # View task details (e.g., bd-a1b2)
bd list                          # List all tasks
bd create "Title" -p 0           # Create priority-0 task
bd update <id> --note "text"     # Add note to task
bd update <id> --status done     # Mark task complete
bd dep add <child> <parent>      # Add dependency
```

## claude-planz Commands

| Command | Purpose |
|---------|---------|
| `/cp:init` | Initialize Beads + create research/plans directories |
| `/cp:status` | Show phase progress and ready tasks |
| `/cp:research <task-id>` | Research a task, save to .beads/research/ |
| `/cp:plan <phase-id>` | Plan a phase, save to .beads/plans/ |

## File Conventions

- Research documents: `.beads/research/<task-id>.md`
- Phase plans: `.beads/plans/<phase-id>.md`
- All documents use ISO dates (YYYY-MM-DD)

## Task Hierarchy

Beads uses hash-based IDs with dot notation for hierarchy:

```
bd-a3f8         # Epic/Phase level
bd-a3f8.1       # Task under epic
bd-a3f8.1.1     # Sub-task
bd-a3f8.2       # Another task under same epic
```

When researching or planning, use the parent ID to identify phases and child IDs for individual tasks.

## Workflow

1. **Initialize**: `/cp:init` sets up Beads and creates directories
2. **Create tasks**: Use `bd create` to add epics/phases and tasks
3. **Research**: `/cp:research bd-xxxx` to research individual tasks
4. **Plan**: `/cp:plan bd-xxxx` to create phase implementation plans
5. **Execute**: Work through tasks using `bd ready` to see what's next
6. **Track**: `/cp:status` to see overall progress

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kevinr-security) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
