---
name: context-mesh
description: When to read or update the Context Mesh (intent, decisions, knowledge, evolution). Use when the user asks about context, ADRs, project intent, knowledge base, or where to document decisions or patterns. Use when this capability is needed.
metadata:
  author: filipesalvio-code
---

# Context Mesh

## When to use

- User asks where to document something, or how to use context/intent/decisions/knowledge.
- Before a major build: "Reference context before implementing."
- After a decision or learned pattern: where to write it.

## Structure

| Path | Purpose | Primary owner |
|------|---------|----------------|
| `context/intent/` | Project vision, PRDs | Librarian (3), Architect (2) |
| `context/decisions/` | ADRs | Architect (2), Logic Auditor (6) |
| `context/knowledge/` | Patterns, anti-patterns | Librarian (3), Bug Hunter (5) |
| `context/evolution/` | Changelog, audit history | Documentation (7), Auditors (5/6) |

## When to read

- **Before major work:** Read `context/intent/project-intent.md` and relevant ADRs in `context/decisions/`.
- **When implementing:** Use intent and ADRs as source of truth; do not assume.

## When to write

- **New architectural decision:** Add or update an ADR in `context/decisions/` (e.g. `ADR-003-*.md`).
- **Reusable pattern or pitfall:** Add to `context/knowledge/` (patterns or anti-patterns).
- **Release or audit:** Update `context/evolution/changelog.md` or add audit notes.

## Ralph vs Context

- **`.ralph/`** – Short-term task state (current task, guardrails). Session-scoped; cleared on rotation.
- **`context/`** – Long-term knowledge. Committed to Git; shared across sessions.

Do not duplicate `.ralph` state into `context/` until it is validated and worth keeping.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/filipesalvio-code) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
