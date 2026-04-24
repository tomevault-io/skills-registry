---
name: cpp-engine-core
description: Develop and harden C++ engine core systems with correctness, stability, and performance controls. Use when implementing engine subsystems, refactoring core runtime paths, enforcing contract boundaries, or validating engine-core readiness. Use when this capability is needed.
metadata:
  author: egorfedorov
---

# C++ Engine Core

Use this skill to implement robust C++ engine core changes with strict correctness gates.

## Workflow

1. Define scope and constraints.
- Define subsystem scope, invariants, and API boundaries.
- Capture objective metrics, bounds, and release blockers.

2. Design implementation plan.
- Design module-level changes with safety/performance implications.
- Keep ownership and dependency boundaries explicit.

3. Execute and iterate.
- Implement in small, traceable increments.
- Record run/build context for reproducibility.

4. Validate contract integrity.
- Validate invariants, failure behavior, and benchmark regression gates.
- Treat contract breaches as blockers.

5. Prepare handoff.
- Deliver patch map, risk notes, and verification commands.
- Include exact commands and acceptance criteria.

## Output Contract

Return:

1. `Context`: goals, assumptions, constraints.
2. `Validation`: pass/fail checks and key deltas.
3. `Changes`: concrete file-level updates.
4. `Commands`: commands and expected outputs.
5. `Risks`: unresolved issues and limits.

## References

- `references/workflow.md`: detailed execution flow.
- `references/checklist.md`: sign-off checklist.

## Execution Rules

- Keep decisions measurable and reversible.
- Keep validation criteria explicit before iteration.
- Escalate invariant breaks and regression-risky changes as blockers.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/egorfedorov) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
