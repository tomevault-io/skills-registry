---
name: safe-refactor
description: Structured refactor execution with guardrails, checkpoints, and regression control. Use when code should be reorganized or simplified without behavior regressions. Use when this capability is needed.
metadata:
  author: puukis
---
Refactor in small reversible steps.

1. Define behavior-preserving target and affected boundaries.
2. Create small edits per step and keep call sites compilable.
3. Run focused checks after each step.
4. Keep rollback options explicit (git diff checkpoints).
5. End with verification summary: what changed, what stayed invariant, and what was validated.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/puukis) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
