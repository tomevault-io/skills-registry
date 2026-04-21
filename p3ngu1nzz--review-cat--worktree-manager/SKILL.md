---
name: worktree-manager
description: Manages git worktrees for worker containers (create/remove/cleanup). Use when setting up parallel work, repairing worktree state, or cleaning up stale worktrees.
metadata:
  author: p3ngu1nzz
---

# Skill: worktree-manager

## What this skill does

Creates and manages worktrees consistent with the planned WorktreeSystem.

## Guardrails

- Never commit secrets (e.g. `.env`, `STATE.json`).
- Prefer deterministic worktree naming and cleanup.

## Related

- `docs/specs/dev/systems/WorktreeSystem.md`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/p3ngu1nzz) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
