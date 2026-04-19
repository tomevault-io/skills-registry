---
name: code-review
description: Practical code review checklist and risk assessment prompts. Use when this capability is needed.
metadata:
  author: dyne
---

# Code Review

Use this checklist to review changes consistently and flag risks.

## Quick scan
- Identify the purpose of the change in one sentence.
- List the files touched and the expected surface area.
- Note any public API or behavior changes.

## Deep review
1. Verify correctness for normal and edge-case inputs.
2. Check error handling and rollback paths.
3. Look for hidden coupling or assumptions.

## Tests
- Confirm that new behavior is covered by tests.
- Ensure existing tests still pass and remain relevant.
- Suggest minimal test additions when coverage is thin.

## Release readiness
- Call out backward compatibility risks explicitly.
- Summarize potential monitoring or rollback steps.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/dyne) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
