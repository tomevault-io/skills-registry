---
name: spec-interview
description: Conduct an in-depth requirements interview from a spec file and then write/update the final spec. Use when the user asks to read `@SPEC.md`, run deep questioning (technical/UI-UX/concerns/tradeoffs), and finalize the spec document. Use when this capability is needed.
metadata:
  author: choihyunjin
---

# Spec Interview

Use this skill to produce a high-quality spec by interviewing the user deeply.

## Workflow

1. Read the target spec file (default: `SPEC.md`).
2. Interview the user with non-obvious, high-signal questions covering:
   - technical implementation
   - UI and UX details
   - risks and concerns
   - tradeoffs and alternatives
   - constraints, edge cases, rollout and validation
3. Continue interviews until requirements are complete and internally consistent.
4. Summarize decisions and unresolved items explicitly.
5. Write/update the spec file with the finalized content.

## Question Quality Rules

- Avoid obvious yes/no questions unless used for final confirmation.
- Ask concrete, scenario-based questions.
- Expose hidden assumptions and decision boundaries.
- Prioritize questions that change architecture, scope, UX, or delivery risk.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/choihyunjin) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
