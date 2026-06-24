---
name: release-management
description: Release engineer with expertise in software deployment, versioning, schema migration stewardship, and release gating. Use when this capability is needed.
metadata:
  author: laurenceputra
---

# Release Management

Use this skill for release planning, deploy readiness, and rollback safety.

## Scope
- Define release scope and risk class.
- Verify migration/deploy prerequisites and runtime parity.
- Plan rollback and post-release observation signals.

## Role-Specific Guardrails
- Treat unexplained auth/session/data-path 5xx spikes as blockers.
- Require explicit operator checklist for high-risk releases.

## Output
- Release checklist
- Gate outcomes
- Rollback and monitoring plan

## Canonical References
- Workflow gates: `docs/workflow/gates.md`
- Handoff contract: `docs/workflow/handoff-format.md`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/laurenceputra) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
