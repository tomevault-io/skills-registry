---
name: test-module
description: Run tests for a specific module and report results Use when this capability is needed.
metadata:
  author: harrytran998
---

Run tests for the `$ARGUMENTS[0]` module:

1. Find test files: `packages/server/src/modules/$0/**/__tests__/**/*.test.ts`
2. Run: `moon run server:test -- --filter $0`
3. If tests fail, read the failing test files and the source they test
4. Report: total tests, passed, failed, with failure details

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/harrytran998) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
