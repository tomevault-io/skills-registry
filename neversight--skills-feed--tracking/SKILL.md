---
name: tracking
description: > Use when this capability is needed.
metadata:
  author: neversight
---

## Prerequisites

- **Load maestro-core first** - [maestro-core](../maestro-core/SKILL.md) for routing table and fallback policies
- Routing and fallback policies are defined in [AGENTS.md](../../AGENTS.md).

# Tracking - Persistent Memory for AI Agents

Graph-based issue tracker that survives conversation compaction. Provides persistent memory for multi-session work with complex dependencies.

## Entry Points

| Trigger | Reference | Action |
|---------|-----------|--------|
| `bd`, `beads` | `references/workflow.md` | Core CLI operations |
| `fb`, `file-beads` | `references/FILE_BEADS.md` | File beads from plan → auto-orchestration |
| `rb`, `review-beads` | `references/REVIEW_BEADS.md` | Review filed beads |

## Quick Decision

**bd vs TodoWrite**:
- "Will I need this in 2 weeks?" → **YES** = bd
- "Could history get compacted?" → **YES** = bd
- "Has blockers/dependencies?" → **YES** = bd
- "Done this session?" → **YES** = TodoWrite

**Rule**: If resuming in 2 weeks would be hard without bd, use bd.

## Essential Commands

| Command | Purpose |
|---------|---------|
| `bd ready` | Show tasks ready to work on |
| `bd create "Title" -p 1` | Create new task |
| `bd show <id>` | View task details |
| `bd update <id> --status in_progress` | Start working |
| `bd update <id> --notes "Progress"` | Add progress notes |
| `bd close <id> --reason completed` | Complete task |
| `bd dep add <child> <parent>` | Add dependency |
| `bd sync` | Sync with git remote |

## Session Protocol

1. **Start**: `bd ready` → pick highest priority → `bd show <id>` → update to `in_progress`
2. **Work**: Add notes frequently (critical for compaction survival)
3. **End**: Close finished work → `bd sync` → `git push`

## Reference Files

| Category | Files |
|----------|-------|
| **Workflows** | `workflow.md`, `WORKFLOWS.md`, `FILE_BEADS.md`, `REVIEW_BEADS.md` |
| **CLI** | `CLI_REFERENCE.md`, `DEPENDENCIES.md`, `LABELS.md` |
| **Integration** | `conductor-integration.md`, `VILLAGE.md`, `GIT_INTEGRATION.md` |
| **Operations** | `AGENTS.md`, `RESUMABILITY.md`, `TROUBLESHOOTING.md` |

## Anti-Patterns

- ❌ Using TodoWrite for multi-session work
- ❌ Forgetting to add notes (loses context on compaction)
- ❌ Not running `bd sync` before ending session
- ❌ Creating beads for trivial single-session tasks

## Related

- [maestro-core](../maestro-core/SKILL.md) - Workflow router and skill hierarchy
- [conductor](../conductor/SKILL.md) - Automated beads operations via facade
- [orchestrator](../orchestrator/SKILL.md) - Multi-agent parallel execution

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neversight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
