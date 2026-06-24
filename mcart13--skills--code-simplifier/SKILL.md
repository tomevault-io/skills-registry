---
name: code-simplifier
description: Simplify and refactor code while preserving behavior. Use when the user asks to simplify, refactor, reduce complexity, remove duplication, flatten control flow, or make logic easier to read/maintain without changing behavior. Use when this capability is needed.
metadata:
  author: mcart13
---

# Code Simplifier

## Overview

Simplify existing code by reducing duplication, flattening control flow, and clarifying intent while preserving behavior, audit posture, and tenant isolation.

## Workflow

1. Confirm scope and invariants.
   - State explicitly that behavior must remain unchanged.
   - List any hard constraints (audit logs, RLS, immutability, error semantics, timeouts/retries).
   - If behavior might change, pause and ask for confirmation.

2. Map the current flow.
   - Identify the smallest unit to simplify (function, module, handler).
   - Note duplicated blocks, deep nesting, repeated validations, or inconsistent error handling.

3. Choose minimal, safe transformations.
   - Prefer guard clauses over nested branches.
   - Extract shared logic into helpers with clear names.
   - Consolidate repeated error mapping or validation.
   - Replace ad-hoc flags with enum-style state where it improves clarity.
   - Reduce parameter surface by grouping related inputs into typed objects.

4. Implement incrementally.
   - Keep edits tight; avoid touching unrelated code.
   - Preserve logs/metrics/audit trails and their fields.
   - Preserve request/trace/account propagation.

5. Verify and summarize.
   - Run the smallest relevant checks (typecheck/tests) if feasible.
   - Summarize behavior invariants and what changed structurally.

## Safety Guardrails

- Do NOT change public API shapes, database schema, or message contracts.
- Do NOT alter compliance-relevant behaviors (evidence signing, retention, policy gates) without explicit approval.
- Preserve error types, error codes, and retry semantics.
- Preserve ordering where it affects side effects (DB writes, message publishes, S3 writes).
- If the simplification crosses regulated workflows, flag it and ask for direction before proceeding.

## Common Simplification Patterns

- Guard clauses: invert conditionals to reduce nesting depth.
- Shared core: extract common body from near-identical branches.
- Normalize inputs: convert to a single internal shape early, then branch by type.
- Error handling: centralize mapping to avoid drift.
- Resource handling: wrap open/close logic in a helper to avoid leaks.

## When to Ask a Clarifying Question

- Ambiguous intent or unclear behavior guarantees.
- Missing tests in a risk-sensitive area.
- Potential concurrency or ordering impact.
- Any regulated workflow or audit-critical path.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mcart13) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
