---
name: best-practice-introduction
description: > Use when this capability is needed.
metadata:
  author: mcj-coder
---

# Best-Practice Introduction Planning

## Intent

Introduce new practices in a controlled way that teams can adopt without
destabilising delivery, while preserving enforceable standards.

---

## When to Use

- Introducing new linters, formatters, analyzers, or checks
- Rolling out architectural conventions or repo policy updates
- Standardising patterns across multiple components/teams

---

## Precondition Failure Signal

- New practices are proposed without adoption plan
- Rollout would break existing components immediately
- Teams bypass standards because they feel imposed or unclear
- “Big bang” changes create churn and resistance

---

## Postcondition Success Signal

- Rollout plan is incremental, scoped, and reviewable
- Adoption phases are clear with measurable checkpoints
- Exceptions are documented and time-bounded
- Standards become enforceable rather than aspirational

---

## Process

1. **Source Review**: Audit existing practices and identify gaps or inconsistencies that the new practice addresses.
2. **Implementation**: Draft the incremental adoption plan and define measurable checkpoints.
3. **Verification**: Validate the plan against the "Postcondition Success Signal" and ensure it does not block delivery.
4. **Documentation**: Document the new practice, the rollout plan, and any time-bounded exceptions in the repo or an ADR if it's a major architectural shift.
5. **Review**: Tech Lead and Platform Engineer review the rollout plan for feasibility and alignment.

---

## Example Test / Validation

- Demonstrate an initial failing signal (policy gap) and a phased path to passing
- Validate that early phases do not block delivery while still enforcing progress
- Confirm measurable criteria exist for moving between phases

---

## Common Red Flags / Guardrail Violations

- Introducing standards without team communication and documentation
- Making everything mandatory immediately without remediation runway
- Creating permanent exceptions “for now”
- Adding overlapping tools/checks that duplicate intent (DRY violation)

---

## Recommended Review Personas

- **Tech Lead** – validates intent, scope, and adoption practicality
- **Platform Engineer** – validates enforcement feasibility and maintenance costs
- **Developer Representative** – validates usability without weakening policy

---

## Skill Priority

P2 – Consistency & Governance

---

## Conflict Resolution Rules

- Prefer incremental adoption over big-bang changes (YAGNI)
- Avoid duplicating existing checks/tools; consolidate intent (DRY)
- If enforcement would block delivery, define phased enforcement explicitly

---

## Conceptual Dependencies

- scoped-colocation
- quality-gate-enforcement

---

## Classification

Governance

---

## Notes

Adoption is part of engineering. Unadopted standards are unimplemented standards.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mcj-coder) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
