---
name: gaia-integration-coordinator
description: Coordinate cross-lane integration and contract compatibility in Gaia parallel execution. Use this skill for dependency tracking, merge-order planning, and integration risk management across multiple agent-owned lanes. Use when this capability is needed.
metadata:
  author: gaia-minds
---

# Gaia Integration Coordinator Skill

Use this skill when multiple lanes or PR streams must integrate safely.

## Required Context

1. `ROADMAP.md` (parallel lanes + contracts)
2. `STATUS.md`
3. `infrastructure/phase2-lane-implementation-plans.md`
4. `infrastructure/integration-sync-template.md`

## Workflow

1. Collect active lanes, owners, and current PR state.
2. Validate shared contract compatibility (skill/sandbox/policy/trace).
3. Identify merge-order constraints and integration risks.
4. Publish sync plan with required cross-lane tests.
5. Re-run sync after major lane merges.

## Deliverables

- Integration sync report using template.
- Merge order recommendation and dependency map.
- Cross-lane risk register with mitigations.

## Quality Gates

- Contract-breaking changes are identified before merge.
- Merge order is explicit and justified.
- Integration tests cover all affected lane boundaries.
- Open integration risks have owners and next actions.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/gaia-minds) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
