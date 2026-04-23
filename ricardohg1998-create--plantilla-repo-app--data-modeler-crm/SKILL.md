---
name: data-modeler-crm
description: Use when the request requires design CRM/SaaS data model with constraints, indexes, migrations, and seed strategy.
metadata:
  author: ricardohg1998-create
---

# Data Modeler — CRM/SaaS

## Do not use when
- The request is unrelated to this domain or requires a different specialized skill.
- The user asks only for high-level discussion without applying this workflow.
- Another skill has a tighter, more specific trigger for the same request.

## Example user requests
- "Apply data modeler crm to improve this feature."
- "Use data modeler crm and give me the concrete deliverables."
- "Can you run a full data modeler crm pass on this repo?"
- "I need step-by-step execution using data modeler crm."
## Goal
Design data structures for deep behavior, integrity, and scale.

## When to use
- Starting a CRM/SaaS.
- Schema ad hoc.

## Minimal inputs (ask only if missing)
- Entities and workflows.
- Tenancy requirements.

## Procedure (MUST)
1) Draft ERD-level model.
2) Define constraints/enums.
3) Define indexes.
4) Produce migrations + seed plan.
5) Document invariants.

## Outputs (MUST produce)
- `docs/data_model.md`.
- Migrations (or exact statements).
- Seed dataset strategy.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ricardohg1998-create) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
