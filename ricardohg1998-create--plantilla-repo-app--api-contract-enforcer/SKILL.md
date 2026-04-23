---
name: api-contract-enforcer
description: Use when the request requires define API contracts and enforce via validation/contract tests and client typing.
metadata:
  author: ricardohg1998-create
---

# API Contract Enforcer

## Do not use when
- The request is unrelated to this domain or requires a different specialized skill.
- The user asks only for high-level discussion without applying this workflow.
- Another skill has a tighter, more specific trigger for the same request.

## Example user requests
- "Apply api contract enforcer to improve this feature."
- "Use api contract enforcer and give me the concrete deliverables."
- "Can you run a full api contract enforcer pass on this repo?"
- "I need step-by-step execution using api contract enforcer."
## Goal
Prevent unstable integrations by defining and validating API contracts early.

## When to use
- Any API work.
- Webhooks/integrations.

## Minimal inputs (ask only if missing)
- REST/OpenAPI vs GraphQL.
- Core resources/ops.

## Procedure (MUST)
1) Define contract.
2) Add examples.
3) Generate types.
4) Add contract validation.
5) Document versioning.

## Outputs (MUST produce)
- `docs/api_contract.*`.
- Contract validation/tests.
- Client typing notes.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ricardohg1998-create) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
