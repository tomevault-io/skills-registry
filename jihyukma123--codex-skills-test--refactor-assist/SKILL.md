---
name: refactor-assist
description: Plan safe refactors and code cleanups. Use when asked to "refactor", "clean up", "simplify", or "reduce technical debt". Use when this capability is needed.
metadata:
  author: jihyukma123
---

# Refactor Assist

## Goals

- Improve structure without changing behavior.
- Keep steps small and reversible.
- Preserve compatibility and test coverage.

## Workflow

1. Identify intent: what should stay the same and what can change.
2. Map dependencies: callers, data contracts, and side effects.
3. Propose a stepwise plan with checkpoints.
4. List safety nets: tests to add or run before/after.
5. Highlight high-risk areas and rollback strategy.

## Output Format

- **Intent**: short statement of non-goals and constraints.
- **Plan**: numbered steps with expected outcome.
- **Risks**: bullets with mitigations.
- **Tests**: minimal set to confirm no behavior change.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jihyukma123) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
