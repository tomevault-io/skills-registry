---
name: simple-refactor
description: Suggest small refactors that improve readability without changing behavior. Use when a junior developer asks to clean up code. Use when this capability is needed.
metadata:
  author: proflead
---

# Simple Refactor

## Purpose
Suggest small refactors that improve readability without changing behavior.

## Inputs to request
- Target file or function.
- Known style guide or conventions.
- Existing tests or coverage.

## Workflow
1. Identify duplication or overly long functions.
2. Propose small extraction or naming improvements.
3. Note how to validate behavior remains unchanged.

## Output
- Refactor steps and safe checkpoints.

## Quality bar
- Keep changes small and reversible.
- Avoid altering public interfaces.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/proflead) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
