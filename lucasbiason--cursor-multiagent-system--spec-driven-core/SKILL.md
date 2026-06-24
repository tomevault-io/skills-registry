---
name: spec-driven-core
description: Processo universal e repetível para criar especificações a partir de qualquer input (texto, docs, código). Usado em Plan mode. Use when this capability is needed.
metadata:
  author: lucasbiason
---

# Spec Driven Core

## Purpose

Provide a universal, repeatable process to create specifications from any input: text, documents, code, diagrams or conversations. This skill is educational and operational.

## When to Use

- Starting a new project or feature
- Improving an existing project
- Planning technical documentation or backlog
- User explicitly enters **Plan mode**

Do NOT jump to implementation. Do NOT write production code unless explicitly requested after specs are approved.

## Input Types Supported

- Plain text, existing documentation, code repositories
- Partial specs, meeting notes, ideas or rough concepts

## Mandatory Phases

### Phase 0 — Input Analysis
- Identify what the input is and what it is NOT
- Identify missing context and assumptions
- **Output:** Input Summary, Known Unknowns

### Phase 1 — Context Specification
Answer explicitly: What exists today? Why does this exist? Who uses it? What problem does it solve?
- **Create:** system-context.md, problem-statement.md, glossary.md

### Phase 2 — Requirements Extraction
Convert intent into requirements. Rules: each requirement must be observable; no implementation details; no UI assumptions.
- **Create:** prd.md, functional-requirements.md

### Phase 3 — Acceptance Definition
For each requirement: define success, failure, edge cases. Use Given/When/Then.
- **Create:** acceptance-criteria.md

### Phase 4 — Constraints (NFR)
Define limits and obligations: performance, security, availability, compliance, observability. Each must include verification method.
- **Create:** non-functional-requirements.md

### Phase 5 — Design Translation
Translate requirements into structure. Rules: design serves requirements; trade-offs must be explicit; decisions must be logged (ADRs).
- **Create:** architecture.md, api-contracts.md, data-model.md, ADRs

### Phase 6 — Validation
Verify traceability, completeness, no hidden assumptions, no vague language.
- **Create/update:** open-questions.md, assumptions.md, risks.md

## Mental Model (order)

1. **Intent** — Why does this system or feature exist?
2. **Scope** — What is included and explicitly excluded?
3. **Actors** — Who or what interacts with the system?
4. **Capabilities** — What the system must do (functional)
5. **Constraints** — How well it must do it (non-functional)
6. **Interfaces** — How parts communicate (APIs, UI, events)
7. **Verification** — How we know it works (tests, acceptance criteria)

Never skip a layer.

## Interaction Rules

- Ask precise questions; explain why each step exists
- Teach by example; never overwhelm; proceed incrementally
- If information is missing: mark as UNKNOWN and add Open Question (OQ-xxx)
- Never invent requirements or business intent

## Default Output Structure

```
spec/ (or docs/specs/)
  00-context/
  10-requirements/
  20-design/
  30-features/
  90-decisions/
  99-meta/
```

## Completion Criteria

A spec is complete when:
- A developer could implement without guessing
- A tester could validate independently
- A new team member could understand intent

## Route to Auxiliary Skills

- Text/notes → requirements-extractor
- FRs exist → acceptance-writer
- Design trade-off → adr-writer
- Legacy code/repo → legacy-specifier
- Messy docs → spec-normalizer

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/lucasbiason) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
