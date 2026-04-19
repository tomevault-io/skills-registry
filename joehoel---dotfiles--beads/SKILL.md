---
name: beads
description: > Use when this capability is needed.
metadata:
  author: joehoel
---

# Beads - Persistent Task Memory for AI Agents

Graph-based issue tracker that survives conversation compaction. Provides persistent memory for multi-session work with complex dependencies.

## bd vs TodoWrite

| bd (persistent) | TodoWrite (ephemeral) |
|-----------------|----------------------|
| Multi-session work | Single-session tasks |
| Complex dependencies | Linear execution |
| Survives compaction | Conversation-scoped |
| Git-backed, team sync | Local to session |

**Decision test**: "Will I need this context in 2 weeks?" → YES = bd

**When to use bd**:
- Work spans multiple sessions or days
- Tasks have dependencies or blockers
- Need to survive conversation compaction
- Exploratory/research work with fuzzy boundaries
- Collaboration with team (git sync)

**When to use TodoWrite**:
- Single-session linear tasks
- Simple checklist for immediate work
- All context is in current conversation
- Will complete within current session

## Prerequisites

```bash
bd --version  # Requires v0.34.0+
```

- **bd CLI** installed and in PATH
- **Git repository** (bd requires git for sync)
- **Initialization**: `bd init` run once (humans do this, not agents)

## CLI Quick Reference

### Essential Commands

```bash
# Find ready work (no blockers)
bd ready --json

# Create new issue
bd create "Issue title" -t bug|feature|task -p 0-4 -d "Description" --json

# Update issue status
bd update <id> --status in_progress --json

# Complete work
bd close <id> --reason "Done" --json

# Show issue details
bd show <id> --json

# List issues
bd list --json

# Sync to git (always run at session end)
bd sync
```

### Dependencies

```bash
# Add dependency (child blocks on parent)
bd dep add <child> <parent>

# Link discovered work
bd dep add <discovered-id> <parent-id> --type discovered-from

# Show dependency tree
bd dep tree <id>

# Find blocked issues
bd blocked
```

### Issue Types

- `bug` - Something broken that needs fixing
- `feature` - New functionality
- `task` - Work item (tests, docs, refactoring)
- `epic` - Large feature composed of multiple issues
- `chore` - Maintenance work (dependencies, tooling)

### Priorities

- `0` - Critical (security, data loss, broken builds)
- `1` - High (major features, important bugs)
- `2` - Medium (nice-to-have features, minor bugs)
- `3` - Low (polish, optimization)
- `4` - Backlog (future ideas)

### Dependency Types

- `blocks` - Hard dependency (issue X blocks issue Y)
- `related` - Soft relationship (issues are connected)
- `parent-child` - Epic/subtask relationship
- `discovered-from` - Track issues discovered during work

Only `blocks` dependencies affect the ready work queue.

## Session Protocol

1. `bd ready --json` — Find unblocked work
2. `bd show <id> --json` — Get full context
3. `bd update <id> --status in_progress --json` — Start work
4. Add notes as you work (critical for compaction survival)
5. `bd close <id> --reason "..." --json` — Complete task
6. `bd sync` — Persist to git (always run at session end)

## Hierarchical Issues (Epics)

For large features, use hierarchical IDs:

```bash
# Create epic (generates parent hash ID)
bd create "Auth System" -t epic -p 1 --json
# Returns: bd-a3f8e9

# Create child tasks (auto-numbered .1, .2, .3)
bd create "Design login UI" -p 1 --json       # bd-a3f8e9.1
bd create "Backend validation" -p 1 --json    # bd-a3f8e9.2
bd create "Integration tests" -p 1 --json     # bd-a3f8e9.3

# View hierarchy
bd dep tree bd-a3f8e9
```

## Hash-Based IDs

bd uses hash-based IDs (e.g., `bd-a1b2`, `bd-f14c`) to prevent collisions when multiple agents/branches work concurrently. This is different from sequential IDs - each issue gets a unique hash that won't conflict on merge.

## Time-Based Scheduling

```bash
# Create with due date and defer
bd create "Task" --due=+6h --json              # Due in 6 hours
bd create "Task" --defer=tomorrow --json       # Hidden until tomorrow

# Query by time
bd list --deferred --json          # Show deferred issues
bd list --overdue --json           # Due date in past
bd list --due-before=+2d --json    # Due within 2 days
```

## Landing the Plane

When finishing a session, always:

1. Close finished issues with `bd close <id> --reason "..."`
2. File any remaining work as new issues
3. Run `bd sync` to persist to git
4. If requested, push to remote: `git push`

## Git Integration

bd automatically:
- **Exports** to JSONL after CRUD operations (30-second debounce)
- **Imports** from JSONL when it's newer than DB (e.g., after `git pull`)

For immediate sync without waiting:
```bash
bd sync  # Force export/import/commit/push
```

## Commit Message Convention

Include issue ID in commit messages for traceability:
```bash
git commit -m "Fix auth validation bug (bd-abc)"
```

## Best Practices for Agents

- Always use `--json` flags for programmatic use
- Link discoveries with `discovered-from` to maintain context
- Check `bd ready --json` before asking "what next?"
- Run `bd sync` before ending sessions
- Use `bd dep tree` to understand complex dependencies
- Priority 0-1 issues are usually more important than 2-4

## Advanced Features

| Feature | CLI | Description |
|---------|-----|-------------|
| Molecules (templates) | `bd mol --help` | Proto definitions, component labels |
| Chemistry (pour/wisp) | `bd pour`, `bd wisp` | Task decomposition patterns |
| Agent beads | `bd agent --help` | Agent bead tracking |
| Async gates | `bd gate --help` | Human-in-the-loop gates |
| Worktrees | `bd worktree --help` | Parallel development patterns |

## Troubleshooting

### Database feels stale after git pull
```bash
bd ready --json  # Auto-imports fresh data
# or explicit:
bd sync
```

### Multiple projects
Each project is isolated with its own `.beads/` directory. bd auto-discovers based on current directory.

### Protected branches
```bash
bd init --branch beads-metadata  # Commit to separate branch
```

## Full Documentation

- **bd prime**: AI-optimized workflow context (run `bd prime`)
- **GitHub**: https://github.com/steveyegge/beads
- **FAQ**: https://github.com/steveyegge/beads/blob/main/docs/FAQ.md

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/joehoel) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
