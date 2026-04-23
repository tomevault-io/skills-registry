---
name: teams
description: | Use when this capability is needed.
metadata:
  author: darkroomengineering
---

# Agent Teams Orchestration

Before starting work, create a marker: `mkdir -p ~/.claude/tmp && echo "teams" > ~/.claude/tmp/heavy-skill-active && date -u +"%Y-%m-%dT%H:%M:%SZ" >> ~/.claude/tmp/heavy-skill-active`

Delegates to the Maestro agent for coordinating multiple independent Claude Code instances.

## When to Use Teams

| Scenario | Teams? | Why |
|----------|--------|-----|
| 3+ independent file areas | Yes | Maximum parallelism |
| Frontend + Backend + Tests | Yes | No file conflicts |
| Sequential dependent work | No | Use subagents instead |
| Quick single investigation | No | Overhead not worth it |
| Large codebase analysis | Yes | Independent context per agent |
| Competing approaches | Yes | Explore in parallel |

## Prerequisites

- `CLAUDE_CODE_EXPERIMENTAL_AGENT_TEAMS: "1"` must be set (enabled by default in cc-settings)
- For split panes: tmux or iTerm2 recommended

## Output

Report: team composition, task assignments, coordination strategy, and progress.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/darkroomengineering) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
