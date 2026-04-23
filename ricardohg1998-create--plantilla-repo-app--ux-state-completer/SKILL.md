---
name: ux-state-completer
description: Use when the request requires implement complete UX states and recovery paths for critical flows.
metadata:
  author: ricardohg1998-create
---

# UX State Completer

## Do not use when
- The request is unrelated to this domain or requires a different specialized skill.
- The user asks only for high-level discussion without applying this workflow.
- Another skill has a tighter, more specific trigger for the same request.

## Example user requests
- "Apply ux state completer to improve this feature."
- "Use ux state completer and give me the concrete deliverables."
- "Can you run a full ux state completer pass on this repo?"
- "I need step-by-step execution using ux state completer."
## Goal
Eliminate happy-path demos by implementing full state matrices and recovery with domain microcopy.

## When to use
- New screen/flow.
- Missing error/empty/loading/success.

## Minimal inputs (ask only if missing)
- Top 3 flows and expected states.

## Procedure (MUST)
1) Define state matrix.
2) Implement loading/empty/error/success + recovery.
3) Add permission denied + timeout.
4) Ensure domain-specific microcopy.
5) Capture proof.

## Outputs (MUST produce)
- State-complete flows.
- Proof references in walkthrough.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ricardohg1998-create) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
