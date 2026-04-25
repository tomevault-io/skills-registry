---
name: requesting-code-review
description: > Use when this capability is needed.
metadata:
  author: mcj-coder
---

# Requesting Code Review

## Intent

Make reviews effective by providing context, evidence, and a clear checklist
before asking for feedback.

---

## When to Use

- Before submitting a PR.
- After completing a major task or batch.
- When requesting architectural guidance.

---

## Precondition Failure Signal

- Review requested without verification evidence.
- Reviewer lacks context or specific areas of concern.
- "LGTM" given without objective criteria.

---

## Postcondition Success Signal

- Review request includes goal, scope, risks, and evidence.
- Reviewer knows what to focus on.
- Review checklist is explicit.

---

## Process

1. **Self-Review**: Inspect VCS diff and run `verification-and-handover`.
2. **Prepare Summary**:
   - Goal and scope.
   - Impacted areas.
   - Verification commands and results.
   - Known risks or concerns.
3. **Request Review**:
   - Provide context and links to relevant files.
   - Ask for review of specific areas.
4. **Track Response**: Record feedback for `receiving-code-review`.

---

## Example Test / Validation

- Review request includes test command output and a focused checklist.

---

## Common Red Flags / Guardrail Violations

- Requesting review without running tests.
- Asking for review with vague "please check".
- Hiding known risks.

---

## Recommended Review Personas

- **Tech Lead** - validates architecture and risk.
- **Software Engineer** - validates correctness and maintainability.

---

## Skill Priority

P2 - Consistency & Governance

---

## Conflict Resolution Rules

- Verification evidence is mandatory before requesting review.

---

## Conceptual Dependencies

- verification-and-handover
- structured-review-workflow

---

## Classification

Governance  
Operational

---

## Notes

Good review requests save time for everyone.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mcj-coder) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
