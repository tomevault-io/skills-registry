---
name: maintain-execution-doc
description: Use when the user asks to create or update a project’s Execution Doc during active development. Goal: keep the Execution Doc aligned with the current iteration state and the design baseline as implementation evolves.
metadata:
  author: iplaylf2
---

# Maintain Execution Doc

Create or update a snapshot **Execution Doc** that reflects the current iteration state.

## Response Contract

- Deliverable: apply the requested changes to the target artifact.
- Chat output: no additional output beyond the deliverable.

## Execution Doc Model

### Execution Doc

An **Execution Doc** is a snapshot document used during active development to reflect the current iteration state and coordination context.

It is rewritten as work evolves.

### Phase

A **Phase** labels the dominant objective of the current work.

When multiple objectives are present, choose the phase that matches the current dominant objective.

- **Build — Make it work**
  Implement the core behavior so the main path is wired through the code, stabilizing the basic interface shape.

- **Prove — Make it correct**
  Increase correctness confidence and reproducibility through verification and fixes.

- **Operate — Make it operational**
  Make the change run across system and runtime boundaries through integration and test-environment execution.

- **Ship — Make it shippable**
  Converge to merge and delivery readiness by closing deliverable artifacts and user-facing content.

## Editing Standards

Apply these standards throughout the update. Each standard is single-sourced here and referenced elsewhere by its ID.

- **phase.locality — Current phase in focus**
  The Execution Doc names Build, Prove, Operate, and Ship and clearly indicates the current Phase. Concrete status and actions are written for the current phase. Other phases may be mentioned briefly as direction or intent, without detailed execution steps.

- **state.relevance — Keep only current-relevant state**
  Keep only iteration state that still affects the current work. Remove completed-work narration and historical detail that no longer changes current implementation, verification, integration, delivery, or handoff.

- **reality.evidence — Anchor key claims**
  Treat the Execution Doc as a statement of current reality. Key completion or readiness claims are backed by a verifiable pointer to the supporting artifact.

- **design.delta — Record deltas from design**
  Do not restate the design baseline. Record only changes relative to it, including deviations, new constraints, and newly discovered facts, and indicate their impact.

## Workflow

1. Rewrite the Execution Doc as a current snapshot of the iteration state.
2. Update the Phase selection and keep detail concentrated on the current phase. Apply `phase.locality`.
3. Remove iteration state that no longer affects the current work. Apply `state.relevance`.
4. Update key current-state claims and attach evidence pointers to key assertions. Apply `reality.evidence`.
5. Update deltas relative to the design baseline and remove obsolete deltas. Apply `design.delta`.
6. Run acceptance checks.

## Acceptance Criteria

A revision is complete only if all checks pass.

- **Response**: Output satisfies the Response Contract.
- **Standards satisfied**: `phase.locality`, `state.relevance`, `reality.evidence`, `design.delta`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/iplaylf2) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
