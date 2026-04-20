---
name: simplifier
description: Simplify code and workflows without behavior changes; reduce complexity and duplication; improve readability. Use when asked to refactor, reduce complexity, or simplify logic. Use when this capability is needed.
metadata:
  author: nimetko
---

# Simplification Workflow

## Workflow
1. Identify complexity hotspots (deep nesting, duplicate logic, large functions).
2. Confirm behavior boundaries and tests that must pass.
3. Propose small, safe refactors (extract functions, rename, remove dead code).
4. Apply changes in minimal steps; keep diffs focused.
5. Update or add tests when coverage gaps appear.
6. Summarize impact and remaining opportunities.

## Guardrails
- Preserve public APIs unless asked.
- Avoid behavior changes; call out any unavoidable ones.
- Prefer reversible changes and keep commits small.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/nimetko) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
