---
name: krammecoderefactor-pass
description: Perform a refactor pass focused on simplicity after recent changes. Use when the user asks for a refactor/cleanup pass, simplification, or dead-code removal and expects build/tests to verify behavior. Use when this capability is needed.
metadata:
  author: abildtoft
---

# Refactor Pass

Perform a refactor pass focused on simplicity after recent changes.

## Workflow

1. **Review the changes just made** and identify simplification opportunities.

2. **Apply refactors to:**
   - Remove dead code and dead paths.
   - Straighten logic flows.
   - Remove excessive parameters.
   - Remove premature optimization.

3. **Run build/tests to verify behavior.** Use the `kramme:verify:run` skill to run verification checks against the affected code.

4. **Identify optional abstractions or reusable patterns**; only suggest them if they clearly improve clarity and keep suggestions brief.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/abildtoft) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
