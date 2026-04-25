---
name: walking-skeleton-delivery
description: > Use when this capability is needed.
metadata:
  author: mcj-coder
---

# Walking Skeleton Delivery

## Intent

Deliver the thinnest viable end-to-end slice that proves the system architecture
works in practice, with tests and quality gates in place before expansion.

---

## When to Use

- Bootstrapping a greenfield system before feature expansion.
- Validating a brownfield migration path with a minimal slice.
- Proving multi-component connectivity and health before scaling.

---

## Precondition Failure Signal

- Architecture decisions are unproven in a running system.
- Tests or health checks are missing for critical paths.
- Implementation expands before an end-to-end slice is validated.

---

## Postcondition Success Signal

- A minimal, end-to-end scenario runs successfully.
- Health checks and basic contracts are verified.
- Testing strategy is applied and documented for the slice.

---

## Process

1. **Source Review**: Identify the smallest end-to-end scenario and dependencies.
2. **Design**: Define the minimal flow and required contracts (use
   `contract-consistency-validation`) and obtain explicit user approval for
   the minimal scenario.
3. **Implementation**: Build only the minimal slice required to prove the flow.
4. **Verification**: Apply the repo's testing strategy and confirm health checks
   are green without warnings.
5. **Documentation**: Record the validated scenario and any ADRs required.
6. **Review**: Tech Lead and Software Engineer review scope discipline.

---

## Example Test / Validation

- A single end-to-end scenario passes with health checks green and tests in
  the appropriate tiers.

---

## Common Red Flags / Guardrail Violations

- Expanding feature scope before the slice is verified.
- Skipping tests or health checks for the initial slice.
- Declaring success without evidence of end-to-end execution.

---

## Recommended Review Personas

- **Tech Lead** - validates scope discipline and architectural intent.
- **Software Engineer** - validates test coverage and implementation clarity.

---

## Skill Priority

P1 - Quality & Correctness

---

## Conflict Resolution Rules

- If delivery pressure conflicts with verification, verification wins.
- Scope expansion requires new approval and planning.

---

## Conceptual Dependencies

- test-driven-development
- contract-consistency-validation
- quality-gate-enforcement
- documentation-as-code

---

## Classification

Core
Operational

---

## Notes

This skill proves architecture in practice before scaling feature delivery.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mcj-coder) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
