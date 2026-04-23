---
name: agent-swarm-spawner
description: Prepares isolated sub-workspaces for parallel agent execution. Copies context and generates specific mission instructions for "Worker" agents. Use when this capability is needed.
metadata:
  author: sounder25
---

# SKILL-009: Agent-Swarm Spawner

## Overview

Complex tasks require parallel execution. This skill orchestrates "Swarming" by creating isolated sub-directories (`./.swarm/task_01`) initialized with all necessary context (`MISSION.md`, `WORKSPACE_PROFILE.json`), allowing a secondary agent to pick up the task immediately.

## Trigger Phrases

- `spawn agent for <task>`
- `create subtask <name>`
- `delegate <task>`

## Inputs

| Parameter | Type | Required | Default | Description |
|-----------|------|----------|---------|-------------|
| `--task-name` | string | Yes | - | Name of the sub-task (e.g. "audit-auth") |
| `--instructions` | string | Yes | - | specific goal for the worker |
| `--context-files` | string[] | No | - | Additional files to copy to swarm dir |

## Outputs

1. `./.swarm/<task_name>/MISSION.md`: The prompt for the worker agent.
2. `./.swarm/<task_name>/`: An isolated workspace ready for the agent.

## Implementation

See `spawn_agent.ps1`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sounder25) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
