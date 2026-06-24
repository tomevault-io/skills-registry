---
name: agent-relay-local-handoff
description: Resume or hand off repositories that already use a local code relay workflow centered on AGENTS.md and orchestrator documents such as BOOT.md, PROGRAM.md, CHECKPOINT.md, HANDOFF.md, and LEADERBOARD.md. Use when Codex should recover context from repository documents, summarize the trusted best state, or prepare a clean session transition without depending on chat history or repository-local scripts. Use when this capability is needed.
metadata:
  author: bloom-lmh
---

# Agent Relay Local Handoff

## Overview

Use this skill when the repository already has relay documents and the goal is to recover or summarize state from those documents.
This is the read-side companion to [$agent-relay-local-update](../agent-relay-local-update/SKILL.md).

## Workflow

1. Detect the repository root and confirm it contains:
   - `AGENTS.md`
   - `orchestrator/`
2. Read `AGENTS.md` and follow its boot sequence.
3. Read the active relay documents:
   - `AGENTS.md`
   - `orchestrator/ALWAYS/BOOT.md`
   - `orchestrator/ALWAYS/CORE.md` when present
   - `orchestrator/ALWAYS/DEV-FLOW.md` when present
   - `orchestrator/PROGRAMS/<active>/PROGRAM.md`
   - `orchestrator/PROGRAMS/<active>/workspace/CHECKPOINT.md`
   - `orchestrator/PROGRAMS/<active>/workspace/HANDOFF.md`
   - `orchestrator/PROGRAMS/<active>/workspace/LEADERBOARD.md` when present
4. Consult `STATUS.yml` secondarily if it exists and helps disambiguate current best or current branch state.
5. Summarize:
   - current trusted best
   - current doing
   - current next
   - whether the repository is dirty

## Execution Rules

- Treat relay markdown files as the source of truth.
- Do not require helper scripts.
- Surface the trusted best baseline clearly.
- If the repository has no relay scaffold, hand off to [$agent-relay-local-setup](../agent-relay-local-setup/SKILL.md).
- If the user wants files updated, switch to [$agent-relay-local-update](../agent-relay-local-update/SKILL.md).

## Expected Output

After using this skill, Codex should leave the user with:

- a concise summary of the active program
- the current trusted best result or baseline
- the next recommended action
- the current repository cleanliness state

## References

See [references/usage.md](./references/usage.md) for example prompts.

---
> Source: [bloom-lmh/agent-relay-skills](https://github.com/bloom-lmh/agent-relay-skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
