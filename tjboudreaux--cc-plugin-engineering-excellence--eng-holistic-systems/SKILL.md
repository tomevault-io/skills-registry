---
name: eng-holistic-systems
description: Model the end-to-end system, constraints, and failure modes before touching code; use whenever a change spans multiple layers or unclear requirements. Use when this capability is needed.
metadata:
  author: tjboudreaux
---

# Holistic Systems Thinking

## Intent
- Understand the true user goal, success metrics, and non-negotiable constraints.
- Trace data/control flow across clients, services, contracts, and on-chain/off-chain boundaries.
- Surface hidden couplings, scalability ceilings, and rollback requirements early.

## Inputs to Gather
1. Problem statement in your own words plus measurable success criteria.
2. Existing architecture sketches (runtime, deployment, data lifecycle) and ownership boundaries.
3. Operational constraints: latency/throughput budgets, privacy rules, platform limitations, on-chain gas caps, store policies, etc.
4. Known risks: single points of failure, stale assumptions, migrations in flight.

## Workflow
1. **Map the ecosystem**
   - List upstream/downstream systems, contracts, and user touchpoints affected.
   - Identify invariants that must hold and where they can break.
2. **Reason about change scope**
   - Partition work into independently testable slices with explicit integration seams.
   - Decide where to place new logic (client/server/worker/contract) based on latency, trust, and deployability.
3. **Plan resiliency**
   - Define failure detection signals, graceful degradation, and rollback/feature-flag hooks.
   - Highlight areas needing schema/version negotiation.
4. **Record decisions for execution**
   - Capture the chosen approach, tradeoffs, and open questions inside the ticket/PR description before implementation.

## Verification
- Confirm each requirement maps to at least one planned change and validation step.
- Share the plan with a peer or stakeholder; ensure assumptions and risks are acknowledged.
- Revisit this skill after implementation planning to see if new dependencies appeared; update the plan accordingly.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tjboudreaux) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
