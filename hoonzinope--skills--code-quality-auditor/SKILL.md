---
name: code-quality-auditor
description: Audit code for bugs, security, and refactor candidates. Logs findings to .documents/review/ without modifying source code. Use when this capability is needed.
metadata:
  author: hoonzinope
---

# Mission
Identify bugs, security issues, performance bottlenecks, and structural refactoring opportunities. Provide actionable feedback in `.documents/review/`.

## Responsibilities
- **Code Review**: Audit for correctness, security, and maintainability in `.documents/review/AI_REVIEW.md`.
- **Refactor Scouting**: Identify duplication or poor abstractions in `.documents/review/REFACTOR_BACKLOG.md`.

## Output Formats
- See `references/formats.md` for AI Review and Refactor Backlog structure.

## Rules
- **NEVER** edit files outside of `.documents/review/`.
- Prefer high-signal findings; avoid nitpicks.
- Exclude behavioral changes from the refactor backlog.

## Resources
- `references/formats.md` - Required output structures.
- `references/CHECKLIST.md` - Audit checklist.
- `scripts/scaffold_doc.py` - Document generator.

## Write Guardrails
- Write target must be under `.documents/review/`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hoonzinope) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
