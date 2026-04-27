---
name: jikime-workflow-team
description: > Use when this capability is needed.
metadata:
  author: jikime
---

# Agent Teams Workflow Orchestration

Provides complete workflow patterns for team-based parallel execution in JikiME-ADK.

## Prerequisites

Both conditions must be met for team mode:
- `CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS=1` in environment or settings.json env
- `workflow.team.enabled: true` in `.jikime/config/workflow.yaml`

## Workflow Files

| File | Phase | Purpose |
|------|-------|---------|
| `team-plan.md` | Plan | Parallel SPEC research and design |
| `team-run.md` | Run | Parallel implementation with file ownership |
| `team-debug.md` | Debug | Competing hypothesis investigation |

## Quick Reference

### Team APIs

| API | Purpose |
|-----|---------|
| `TeamCreate(team_name)` | Initialize team structure |
| `Task(subagent_type, team_name, name, prompt)` | Spawn teammate |
| `SendMessage(recipient, type, content)` | Inter-teammate communication |
| `TaskCreate/Update/List/Get` | Shared task coordination |
| `TeamDelete` | Release team resources |

### Mode Selection

- `--team`: Force Agent Teams mode
- `--solo`: Force sub-agent mode
- No flag: Auto-select based on complexity thresholds

### Fallback Behavior

If team mode fails or prerequisites not met:
1. Log warning about team mode unavailability
2. Graceful fallback to sub-agent mode
3. Continue from last completed task
4. No data loss or state corruption

## Bundled Files

- `team-plan.md` - Plan phase team orchestration
- `team-run.md` - Run phase team orchestration
- `team-debug.md` - Debug phase team orchestration

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jikime) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
