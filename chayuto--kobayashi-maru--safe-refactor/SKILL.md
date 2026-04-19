---
name: safe-refactor
description: Safely refactor a module with full test coverage verification Use when this capability is needed.
metadata:
  author: chayuto
---

Safely refactor the specified module or file ($ARGUMENTS). Follow this process:

## 1. Analyze
- Read the target file and all its imports/exports
- Find all callers using Grep
- Read existing tests for this module
- Document current behavior

## 2. Verify Baseline
- Run existing tests: `pnpm run test -- $ARGUMENTS`
- Run full suite: `pnpm run test`
- Note current test count and pass rate

## 3. Refactor
- Make changes incrementally
- Each change must be isolated and reversible
- No breaking API changes unless explicitly requested

## 4. Verify
- Run tests after each change
- Ensure test count has not decreased
- Run `pnpm run lint && pnpm run test && pnpm run build`

## 5. Report
- Summarize what changed and why
- Show before/after metrics (file size, test count)
- Generate report at `docs/change_notes/` if changes were made

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/chayuto) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
