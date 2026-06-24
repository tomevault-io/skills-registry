---
name: requirements-extractor
description: Converte input não estruturado (texto, notas, docs parciais) em requisitos funcionais (FR) e perguntas abertas. Não inventa requisitos. Use when this capability is needed.
metadata:
  author: lucasbiason
---

# Requirements Extractor

## Purpose

Convert unstructured input (plain text, notes, partial docs, meeting summaries) into:
- Functional Requirements (FR-xxx)
- Actors
- Flows (happy path + edge cases)
- Explicit Unknowns and Open Questions
- Assumptions (only if clearly implied)

This skill does NOT design solutions. This skill does NOT write code.

## When to Use

- User provides raw text, tickets, or partial docs
- Plan Lead routes input that is "text/ideas" to this skill

## Inputs Supported

- Plain text notes, existing docs (even messy)
- Tickets / Slack messages / emails
- User story fragments, problem statements

## Output Files (canonical)

Write results into:
- spec/10-requirements/functional-requirements.md
- spec/99-meta/open-questions.md
- spec/99-meta/assumptions.md

If these folders don't exist, propose creating them.

## Extraction Rules (hard constraints)

1. Never invent requirements. If unknown → mark `UNKNOWN` + add an Open Question.
2. Prefer observable behavior over internal implementation.
3. Every requirement must be testable (at least in principle).
4. Avoid UI/tech details unless the input explicitly mandates them.
5. Split combined requirements into atomic ones.

## Process

### Step 0 — Intake Summary
Summarize the input in 5–10 bullets: domain/subject, key capabilities, stakeholders, constraints mentioned, missing context.

### Step 1 — Identify Actors
List actors (human roles, system actors) with goals, typical actions, notes/constraints.

### Step 2 — Extract Functional Requirements
Create FRs with IDs (FR-001, FR-002…). Each FR must include: Title, Description (observable behavior), Primary actor, Preconditions, Success outcome, Failure/exception notes, Priority (P0/P1/P2 or UNKNOWN).

### Step 3 — Group Into Capabilities
Group FRs into capability buckets (CAP-<name>: list FRs).

### Step 4 — Unknowns, Ambiguities, Conflicts
Populate Open Questions (OQ-001, OQ-002…): Question, Why it matters, Suggested answer options (if appropriate), Who likely knows (role).

### Step 5 — Assumptions (optional)
Only add assumptions if clearly implied by the input. Mark as A-001: <assumption>, Risk if false, How to validate.

## Output Format

Use templates: functional-requirements.md with Actors, Capability Map, Requirements (FR-xxx with Description, Primary actor, Preconditions, Success outcome, Failure/exceptions, Priority). Add Traceability Notes (link to PRD items if they exist). open-questions.md and assumptions.md with the structures above.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/lucasbiason) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
