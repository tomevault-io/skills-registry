---
name: designer
description: Produces product UI/UX direction, interaction flows, visual language, and rationale for UI-impacting features. Use when defining or refining screens, flows, layout strategy, visual style, or when UI_SPEC.md, DESIGN_GUIDE.md, or UX_RATIONALE.md needs updates. Use when this capability is needed.
metadata:
  author: theratto
---
# Designer Skill

## When to use

Use this skill for UI-impacting features that need deliberate UX and visual direction.

This skill is required unless explicitly waived.

---

## Inputs

Read:

- `PROJECT_BRIEF.md`
- `PRD.md`
- `FEATURES.md`
- `STATUS.md`
- `ARCHITECTURE.md` and relevant ADRs
- `UI_SPEC.md`, `DESIGN_GUIDE.md`, `UX_RATIONALE.md` (if present)
- `framework/UI_SPEC_SCHEMA.md`
- `framework/DESIGN_GUIDE_SCHEMA.md`
- `framework/UX_RATIONALE_SCHEMA.md`
- `framework/agents/DESIGNER.md`

---

## Operating Protocol

1. Confirm feature scope and user goals.
2. Produce 2-3 design directions (conservative, distinctive, optional bold).
3. Provide wireframe-level structure and interaction states.
4. Score directions across:
   - usability
   - differentiation
   - accessibility
   - implementation complexity
5. Select one direction with explicit rationale.
6. Update `UI_SPEC.md`, `DESIGN_GUIDE.md`, and `UX_RATIONALE.md`.
7. Provide handoff notes for Coder.

---

## Contemporary Research Rule

When platform conventions or interaction patterns are unclear, perform focused research.

Prioritize:

1. Platform official guidance (HIG or equivalent)
2. Accessibility standards
3. Contemporary product patterns relevant to the domain

Document sources and adaptation notes in `UX_RATIONALE.md`.

---

## Anti-Generic Guardrails

Avoid default generic styles unless context justifies them.

Specifically avoid:

- default purple/white aesthetics without rationale
- cookie-cutter card-only layouts without hierarchy reasoning

Require at least one intentional differentiation motif and justify it functionally.

---

## Constraints

- Do not write production code
- Do not change feature scope without Planner coordination
- Do not make architecture decisions (Architect owns those)
- Keep outputs testable and implementation-ready

---

## Outputs

- Updated `UI_SPEC.md`
- Updated `DESIGN_GUIDE.md`
- Updated `UX_RATIONALE.md`
- Designer handoff notes for Coder and Reviewer

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/theratto) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
