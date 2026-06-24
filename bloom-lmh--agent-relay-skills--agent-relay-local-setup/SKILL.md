---
name: agent-relay-local-setup
description: Set up or refresh a local code relay workflow based on AGENTS.md, orchestrator/ALWAYS, program folders, STATUS.yml, SCOPE.yml, CHECKPOINT.md, HANDOFF.md, and optional LEADERBOARD.md. Use when Codex needs to create resumable local handoff files, define write boundaries, map repository context, or choose how handoff updates should happen through manual refresh, pre-commit, post-merge, or post-train triggers. Use when this capability is needed.
metadata:
  author: bloom-lmh
---

# Agent Relay Local Setup

## Overview

Use this skill to create or refine a local, document-first relay workflow inside a repository.
Pair it with [$agent-relay-local-handoff](../agent-relay-local-handoff/SKILL.md) for resume and read-only recovery, and [$agent-relay-local-update](../agent-relay-local-update/SKILL.md) for manual relay refresh.

## Workflow

1. Inspect the repository first.
2. Add the smallest possible relay scaffold without moving the existing project layout.
3. Fill the scaffold with real repository context, not placeholders.
4. Choose the handoff refresh strategy:
   - manual-only
   - pre-commit
   - post-merge
   - post-train
   - a combination of the above
5. If the user wants lifecycle automation and the current session is not in Plan mode, recommend enabling Plan mode before deciding the trigger strategy.
6. Preserve manual notes while allowing machine-managed sections in checkpoint and handoff files.

## Default Local Layout

Use this structure unless the repository already has an equivalent pattern:

```text
AGENTS.md
orchestrator/
  ALWAYS/
    BOOT.md
    CORE.md
    DEV-FLOW.md
    SUB-AGENT.md
    RESOURCE-MAP.yml
  PROGRAMS/
    P-YYYY-001/
      PROGRAM.md
      STATUS.yml
      SCOPE.yml
      workspace/
        CHECKPOINT.md
        HANDOFF.md
        LEADERBOARD.md
```

Do not reorganize the source tree just to match this layout. Wrap the relay scaffold around the existing repository.

## Required Content

- `AGENTS.md`
  - define the boot sequence
  - point to the active program
- `RESOURCE-MAP.yml`
  - map repositories, entrypoints, infrastructure, artifacts, and relationships
- `PROGRAM.md`
  - define the objective and acceptance criteria
- `STATUS.yml`
  - record current best, done/doing/next, and branch status
- `SCOPE.yml`
  - limit write areas for the active task
- `CHECKPOINT.md`
  - preserve trusted best results and ruled-out branches
- `HANDOFF.md`
  - capture what is done, in progress, and recommended next
- `LEADERBOARD.md`
  - optional ranking snapshot of best runs

## Hook Selection Guidance

Choose refresh moments deliberately:

- `manual-only`
  - best when the repository is small or the user prefers explicit control
- `pre-commit`
  - good when `HANDOFF.md` should always describe the exact pre-commit state
- `post-merge`
  - good when relay state should be refreshed after branch syncs
- `post-train`
  - good for ML and benchmark repositories where checkpoint provenance changes after runs

Use hooks only when the repository actually benefits from them. A manual refresh flow plus [$agent-relay-local-update](../agent-relay-local-update/SKILL.md) is often enough.

## Execution Rules

- Keep repository-specific parsing logic in repository-local scripts or docs, not in the skill.
- Preserve manual notes.
- Use machine-managed markers for sections that Codex may replace repeatedly.
- Keep one active program by default unless the user explicitly needs parallel tracks.
- If the repository has no relay scaffold yet, this skill should create it. If it already exists, refresh only the parts that are stale.

## References

See [references/local-mode-layout.md](./references/local-mode-layout.md) for a concise layout and pairing rules.

---
> Source: [bloom-lmh/agent-relay-skills](https://github.com/bloom-lmh/agent-relay-skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
