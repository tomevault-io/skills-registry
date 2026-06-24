---
name: agent-relay-local-update
description: Force-refresh local code relay status documents such as STATUS.yml, CHECKPOINT.md, HANDOFF.md, and LEADERBOARD.md in repositories that already use AGENTS.md and orchestrator-based relay files. Use when Codex should manually update relay documents after code changes, before switching sessions, before a commit, after a training run, or whenever hooks and local automation are absent or intentionally skipped. Use when this capability is needed.
metadata:
  author: bloom-lmh
---

# Agent Relay Local Update

## Overview

Use this skill when the repository already has a local relay scaffold and the user wants a manual, document-first refresh of relay state.
This is the write-side companion to [$agent-relay-local-handoff](../agent-relay-local-handoff/SKILL.md).

## Workflow

1. Confirm the repository contains:
   - `AGENTS.md`
   - `orchestrator/`
   - an active `PROGRAMS/<id>/` folder
2. Read the relay docs:
   - `AGENTS.md`
   - `orchestrator/ALWAYS/BOOT.md`
   - `orchestrator/PROGRAMS/<active>/PROGRAM.md`
   - `orchestrator/PROGRAMS/<active>/workspace/CHECKPOINT.md`
   - `orchestrator/PROGRAMS/<active>/workspace/HANDOFF.md`
   - `orchestrator/PROGRAMS/<active>/workspace/LEADERBOARD.md` when present
3. Inspect current state:
   - branch
   - latest commit
   - `git status --short`
   - trusted best
4. Update the relay docs directly:
   - `STATUS.yml` when current best, branch status, or next step changed
   - `CHECKPOINT.md` when trusted best or ruled-out branches changed
   - `HANDOFF.md` with a fresh session summary
   - `LEADERBOARD.md` only when ranking information is available and reliable
5. Preserve manual notes and replace only machine-managed blocks.

## Execution Rules

- Treat relay docs as the primary interface.
- Do not require helper scripts.
- Be explicit when a branch tied or lost; do not silently promote it.
- If the repository has no relay scaffold, switch to [$agent-relay-local-setup](../agent-relay-local-setup/SKILL.md).

## Expected Output

After using this skill, Codex should leave the repository with:

- refreshed relay documents
- a clear trusted best
- an updated branch status
- a clean next-step handoff

## References

See [references/usage.md](./references/usage.md) for common trigger moments.

---
> Source: [bloom-lmh/agent-relay-skills](https://github.com/bloom-lmh/agent-relay-skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
