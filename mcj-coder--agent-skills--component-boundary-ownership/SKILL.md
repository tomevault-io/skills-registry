---
name: component-boundary-ownership
description: > Use when this capability is needed.
metadata:
  author: mcj-coder
---

# Component Boundary & Ownership Review

## Intent

Ensure functionality is located in the correct boundary with clear ownership,
avoiding premature abstraction and accidental coupling.

---

## When to Use

- Introducing new functionality that might be reused
- Reviewing PRs that move code across boundaries
- Deciding whether something is slice-local or shared
- Resolving duplication across components

---

## Precondition Failure Signal

- Functionality placed in shared areas without proven reuse
- Slice-local logic extracted prematurely “for future reuse”
- Ownership is unclear (who maintains this code?)
- Coupling increases because shared code pulls in unrelated dependencies

---

## Postcondition Success Signal

- Functionality is placed where it best matches current usage and ownership
- Shared abstractions exist only when reuse is real and justified
- Boundaries are explicit and support independent build/test/deploy
- Duplication decisions are intentional and reviewable

---

## Process

1. **Source Review**: Inspect the current code location, its callers, and potential future reuse points.
2. **Implementation**: Move or refactor code to the appropriate boundary (slice-local or shared).
3. **Verification**: Run build and test for the affected and related components to ensure no boundary violations or unintended coupling.
4. **Documentation**: Record the reasoning for placement in an ADR if it defines or changes architectural boundaries.
5. **Review**: Tech Lead and Architecture/Domain Expert review the placement and ownership decision.

---

## Example Test / Validation

- Demonstrate reuse evidence (multiple consumers) before extracting
- Verify moving code does not introduce cross-slice coupling
- Confirm ownership and responsibility are clear post-change

---

## Common Red Flags / Guardrail Violations

- “We might need this elsewhere later”
- Creating shared utilities to avoid small duplication
- Adding dependencies to shared libraries that force consumers to import unrelated concerns
- Moving code from other repos without checking other consumers

---

## Recommended Review Personas

- **Tech Lead** – validates boundaries, ownership, and YAGNI discipline
- **Architecture/Domain Expert** – validates coupling/cohesion and design intent
- **Platform Engineer** – validates build/deploy boundary implications

---

## Skill Priority

P2 – Consistency & Governance

---

## Conflict Resolution Rules

- Default to slice-local unless reuse is proven
- If duplication exists, prefer extracting only the stable, minimal shared portion
- If reuse scope is unclear across repos/projects, escalate before relocation

---

## Conceptual Dependencies

- incremental-change-impact
- scoped-colocation

---

## Classification

Governance

---

## Notes

The goal is not “maximum reuse”; it is “minimum coupling with justified reuse.”

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mcj-coder) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
