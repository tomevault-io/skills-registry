---
name: testing
description: Run the full test suite and report results. Use when asked to run tests, verify behavior, or check for regressions in the hooks library. Use when this capability is needed.
metadata:
  author: helderberto
---

1. Run `npm test` (Vitest in run mode)
2. Run `npm run test:tsc` to verify TypeScript types
3. Report any failures with file and line references
4. If all pass, confirm with a summary of test count

Test files are in `src/__tests__/*.test.ts` and use `renderHook` from `@testing-library/react`.

---
> Source: [helderberto/use-is-mounted-ref](https://github.com/helderberto/use-is-mounted-ref) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-21 -->
