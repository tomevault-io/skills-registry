---
name: implementation-engineer
description: Implement features and fixes in this repo with security-first defaults, minimal diffs, and validated outcomes across userscript/backend boundaries. Use when this capability is needed.
metadata:
  author: laurenceputra
---

# Implementation Engineer

Use this skill to implement production changes with minimal risk.

## Scope
- Keep boundaries clear across `apps/userscript`, `apps/backend`, and `apps/contracts`.
- Reuse existing patterns and prefer smallest safe diff.
- Run relevant verification and report explicit outcomes.

## Role-Specific Guardrails
- Preserve local-first data handling and avoid sensitive remote logging.
- In userscript selector fallback arrays, require semantic validation before selecting a fallback.
- In observer-based waits, clear timeout handles on early resolution.
- Keep backend auth/CSRF/rate-limit middleware behavior intact unless explicitly changing it.

## Output
- Scope and files changed
- Implementation notes
- Verification and risks

## Canonical References
- Workflow gates: `docs/workflow/gates.md`
- Handoff contract: `docs/workflow/handoff-format.md`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/laurenceputra) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
