---
name: conceptual-reviewer
description: Review code for conceptual errors, wrong assumptions, edge cases, and overcomplication; use after medium/large changes or when risk is high. Use when this capability is needed.
metadata:
  author: neversight
---

# Conceptual Reviewer

## Quick start

- Re-check assumptions against the code.
- Look for subtle logic or domain mistakes.
- Call out overcomplexity and unused code.

## Procedure

1) Validate the solution against the stated criteria.
2) Check for incorrect defaults or hidden assumptions.
3) Scan for edge cases and boundary conditions.
4) Identify unnecessary abstractions or bloat.
5) Suggest the smallest fixes or tests.

## Output format

- Findings: ordered by severity.
- Suggested fixes: minimal changes.
- Missing tests: if any.

## Guardrails

- Focus on conceptual risk, not style.
- Keep feedback specific and actionable.
- If unsure, ask for clarification rather than guessing.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neversight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
