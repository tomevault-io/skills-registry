---
name: cockpit-agentbus
description: Operational rules for using AgentBus safely: packet threading, updates, receipts, and close discipline. Use when this capability is needed.
metadata:
  author: future3ooo
---

# Cockpit AgentBus

Use this skill whenever you create, update, or close AgentBus tasks.

## Rules
- Use `send-text` for new work and preserve threading with `rootId` and `parentId`.
- Use `update` to append clarifications to in-flight tasks; do not fork duplicate task ids.
- Close tasks with accurate `outcome` and an auditable `note`.
- Include `commitSha` in receipts when code changes were committed.
- Keep packet/receipt content secret-free.

## Threading guidance
- `USER_REQUEST` should start a new root unless explicitly tied to an existing workflow.
- Follow-ups must keep `signals.rootId` stable for the lifecycle.
- Use `signals.parentId` for direct lineage to the triggering task.

## Outcome discipline
- `done`: acceptance criteria met and verification completed.
- `blocked`: concrete dependency prevents progress.
- `needs_review`: human/reviewer decision still required.
- `failed`: attempted execution failed and needs a fix task.
- `skipped`: intentionally canceled/superseded.

## Learned heuristics (SkillOps)
<!-- SKILLOPS:LEARNED:BEGIN -->
<!-- SKILLOPS:LEARNED:END -->

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/future3ooo) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
