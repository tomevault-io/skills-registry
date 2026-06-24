---
name: design-authoring
description: Translate approved requirements into a minimal, implementable technical design (architecture, interfaces, data shapes, trade-offs) following specs/design.md. Use when this capability is needed.
metadata:
  author: jpiedrafita
---

# design-authoring

Purpose: produce/iterate a design doc that *implements the approved requirements* with minimal noise, ready for task breakdown.

## Scope resolution

- If scope is `root`: use `specs/design.md` (inputs: `specs/requirements.md`)
- If scope is `<slug>`: use `specs/features/<slug>/design.md` (inputs: `specs/features/<slug>/requirements.md`)
- If scope is unclear: ask only for `root` or `<slug>`.

## Inputs

- Approved requirements file for the resolved scope.
- Existing `design.md` for the same scope (if present).

## Output

- Updated `design.md` for the resolved scope, aligned with the repo template.

## Process

1. Read the requirements for the resolved scope.
2. Draft/update `design.md` keeping it minimal and requirements-driven.
3. Ensure every major section maps to REQ IDs (explicitly reference REQ-xxx where useful).
4. Maintain “Trade-offs” and “Open Questions” as first-class sections (keep them short).
5. Stop and ask if requirements are missing/ambiguous (do not invent).

## Design guidelines (minimal)

- Prefer “shapes” and short bullets over large payload examples.
- Prefer interfaces/contracts over implementation details.
- Use diagrams only when they clarify the architecture.

## Required sections (keep concise)

1) **Overview**
- Goal and non-goals (1–3 bullets each)
- Assumptions / constraints (brief)

2) **Architecture**
- Components + responsibilities (bullets)
- Mermaid diagram (only if it improves clarity)
- Data/control flow at a high level

3) **Interfaces**
Include only what applies (remove empty subsections):
- API (REST/GraphQL)
- Events / messaging
- CLI
- UI
- Jobs / schedulers
For each interface: purpose + inputs/outputs (shape) + error handling rules.

4) **Data shapes**
- Key entities/records and their fields at a conceptual level
- Persistence is optional: describe only if the project needs it

5) **Cross-cutting**
- Security
- Observability (logging/metrics/tracing) if relevant
- Performance/scalability notes (only if requirements imply it)
- Testing strategy at a high level (unit/integration boundaries)

6) **Trade-offs**
- 2–5 key decisions max
- For each: Decision + Alternatives + Rationale (short)

7) **Open Questions**
- Unknowns that block tasks or require user decision
- Keep it actionable

## Rules

- Do not introduce new scope beyond approved requirements.
- Do not add long code snippets or full schemas unless the requirements demand it.
- Keep the document template-aligned; delete unused sections rather than leaving placeholders.
- If a decision needs permanence, suggest an ADR (do not auto-create unless asked).

## Final response

- Summarize edits in 3–5 bullets.
- If blocked: list only the missing answers needed to proceed.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jpiedrafita) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
