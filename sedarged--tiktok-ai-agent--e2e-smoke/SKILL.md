---
name: e2e-smoke
description: Run Playwright E2E tests as smoke; summarize results. Use when asked to "run E2E", "smoke test", or "check E2E". Use when this capability is needed.
metadata:
  author: sedarged
---

# E2E Smoke

Run Playwright E2E tests and report pass/fail with failing test names.

## Input

- Scope: "local" (default) or "CI". Local may use existing dev server or start one; CI typically runs in workflow.

## Steps

1. Ensure Playwright browsers are installed: `npx playwright install --with-deps` (or `npx playwright install` on Windows/macOS if sufficient).
2. From repo root: `npm run test:e2e`.
3. Summarize: **pass** or **fail**, and if fail list the failing spec names (and optionally first error lines). Do not modify tests to make them pass; report and optionally suggest implementation fixes.

## Output

- "E2E smoke: pass" or "E2E smoke: fail" plus failing test names and brief error info.

## References

- [docs/testing.md](../../../docs/testing.md) – E2E setup, `test:e2e`
- [apps/web/tests/e2e/](apps/web/tests/e2e/) – E2E specs
- [.github/workflows/ci.yml](.github/workflows/ci.yml) – E2E job

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sedarged) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
