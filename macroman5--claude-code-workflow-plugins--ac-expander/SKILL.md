---
name: ac-expander
description: Turn vague Acceptance Criteria into measurable checks and test assertions Use when this capability is needed.
metadata:
  author: macroman5
---

# Acceptance Criteria Expander

## Purpose
Rewrite ambiguous AC into specific, testable checks and edge cases.

## Behavior
1. For each AC, create measurable statements with inputs/outputs.
2. Add 2–4 edge cases (bounds, invalid, error paths).
3. Suggest test names that map 1:1 to checks.

## Guardrails
- Preserve original intent; show original text and revised version.
- Keep each AC concise (≤3 lines each).

## Integration
- Project Manager agent; `/lazy create-feature` refinement step.

## Example Prompt
> Make these AC measurable and propose matching tests.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/macroman5) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
