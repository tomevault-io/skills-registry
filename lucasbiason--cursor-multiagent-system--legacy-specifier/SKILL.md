---
name: legacy-specifier
description: Extrai especificações de um sistema legado (código existente) quando não há documentação adequada. Use when this capability is needed.
metadata:
  author: lucasbiason
---

# Legacy Specifier

## Purpose

Reverse-engineer specifications from an existing system:
- Identify behaviors from code, routes, UI, tests
- Extract APIs and contracts
- Infer business rules ONLY when strongly evidenced
- Mark uncertainties explicitly

This skill prioritizes accuracy over completeness.

## When to Use

- User asks to extract specs from repo/code
- Plan Lead routes "existing codebase" input to this skill

## Inputs

- Repository code, existing docs (if any)
- API schemas (OpenAPI), test suites, UI flows (routes/pages)

## Outputs

- spec/00-context/system-context.md (update)
- spec/20-design/api-contracts.md
- spec/30-features/<FEATURE-ID>/{spec.md, acceptance.md, test-plan.md}
- spec/99-meta/open-questions.md

## Rules (non-negotiable)

1. Never guess business intent. If unclear → `UNKNOWN` + Open Question.
2. Prefer evidence sources in this order: tests > docs > code paths > UI > comments.
3. Separate: Observed behavior (fact) vs Intended behavior (hypothesis, mark as such).
4. Track "contract surface" carefully: public APIs, payloads, status codes; UI visible behaviors; integrations.

## Process

### Step 0 — System Map
Produce a map: Components (services, frontend, jobs), Entry points (routes, commands), Data stores, Integrations.

### Step 1 — Contract Extraction
For each entry point: Inputs, Outputs, Errors, Auth requirements (if observed), Side effects.

### Step 2 — Behavior Extraction
Identify: Happy path, Validation, Authorization, Idempotency/retries (if present), Pagination/sorting rules, Data boundaries.

### Step 3 — Feature Grouping
Group endpoints + UI flows into feature candidates. Assign FEATURE-001, FEATURE-002…

### Step 4 — Spec Drafting
For each feature: Write spec.md (observed behavior), acceptance.md (provisional if needed), test-plan.md (existing tests + missing ones).

### Step 5 — Unknowns + Risks
List open questions: Intent unclear, Inconsistent behavior across services, Missing tests for critical paths.

## Feature Spec Template (legacy)

# FEATURE-XXX — <name>. Status: LEGACY-DERIVED. Confidence: HIGH | MEDIUM | LOW. Evidence (files/tests/docs). Observed Behavior. Inputs/Outputs. Business Rules (Observed). Edge Cases. Known Gaps/Uncertainties. Acceptance (Provisional if needed) with GWT scenarios.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/lucasbiason) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
