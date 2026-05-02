---
name: refactor-pass
description: Perform a refactor pass focused on simplicity after recent changes. Use when the user asks for a refactor/cleanup pass, simplification, or dead-code removal and expects build/tests to verify behavior. Use when this capability is needed.
metadata:
  author: idmyn
---

# Refactor Pass

## Workflow

Review the changes just made and identify simplification opportunities.
Apply refactors to:
Remove dead code and dead paths.
Straighten logic flows.
Remove excessive parameters.
Remove premature optimization.
Run build/tests to verify behavior.
Identify optional abstractions or reusable patterns; only suggest them if they clearly improve clarity and keep suggestions brief.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/idmyn) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
