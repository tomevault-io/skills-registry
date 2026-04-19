---
name: review-standards
description: Review code or files against project constitution and implementation status. Use before merge or when checking compliance. Use when this capability is needed.
metadata:
  author: madelinben
---

Review the specified code against project constitution and standards.

**Constitution**: @.specify/memory/constitution.md
**Implementation status**: @docs/implementation-status.md

**Checklist**:
- No `as any` or `as unknown`; guard clauses
- Tailwind only; no hardcoded px or !important
- British English, full words
- Design system: generic, no API imports, renderer-presenter where applicable
- Forms: section structure, TanStack Forms
- Run `pnpm run lint` and `pnpm run ts-check`

**Target**: $ARGUMENTS (default: changed files or current directory)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/madelinben) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
