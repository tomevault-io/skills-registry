---
name: gastown
description: | Use when this capability is needed.
metadata:
  author: jeremylongshore
---
# Gastown

## Overview

Gas Town is a multi-agent orchestration system for Claude Code that enables parallel AI workers to execute tasks simultaneously. It provides work tracking through beads, agent lifecycle management via polecats and crew, and automated code merging through the Refinery.

## Prerequisites

- Go 1.21+ installed for CLI tools (`gt` and `bd`)
- Git configured with SSH or HTTPS access
- Terminal access for running commands
- Sufficient disk space for workspace (~100MB for ~/gt)
- GitHub account for repository integration (optional)

## Instructions

1. Install Gas Town CLI tools (gt and bd) using Go
2. Create your workshop directory at ~/gt
3. Run diagnostics with gt doctor and bd doctor
4. Add a project as a rig using gt rig add
5. Create work items as beads using bd create
6. Sling work to agents using gt sling
7. Monitor progress with gt status and gt peek
8. Let the Refinery merge completed work

The Cognition Engine. Track work with convoys; sling to agents.

## Output

- Executed gt and bd commands with results reported to user
- Engine status reports showing system health and worker states
- Work tracking updates (beads created, assigned, completed)
- Polecat and crew lifecycle events (spawn, completion, termination)
- Diagnostic results from gt doctor and bd doctor
- Merge pipeline status from Refinery operations

## Error Handling

See `${CLAUDE_SKILL_DIR}/references/errors.md` for comprehensive error handling.

## Examples

See `${CLAUDE_SKILL_DIR}/references/examples.md` for detailed examples.

## Resources


- Official Gastown documentation
- Community best practices and patterns
- Related skills in this plugin pack

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jeremylongshore) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
