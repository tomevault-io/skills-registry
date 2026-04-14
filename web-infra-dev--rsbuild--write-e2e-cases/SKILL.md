---
name: write-e2e-cases
description: Use when adding or updating Rsbuild end-to-end tests in `e2e/cases`, including new feature coverage, bug reproduction, and regression prevention.
metadata:
  author: web-infra-dev
---

# Write E2E Cases

## Steps

1. Review uncommitted git changes to define test scope and target behavior.

2. Read `e2e/README.md` and follow its conventions.

3. Prefer `@e2e/helper` methods (for example `dev`, `build`) to keep tests minimal.

4. Add Playwright cases under `e2e/cases`, following existing directory patterns.

5. Keep assertions focused and readable; avoid redundant setup and checks.

6. Run `pnpm e2e` to validate.

## Case Structure

- Include a `src` directory in every case (required).
- Add `rsbuild.config.ts` only when needed.
- Split into multiple case directories when cases need different `src` code or different Rsbuild configs.

## Constraints

- If tests can pass only after source-code changes, do not change source code directly. Explain the required source change and ask the user before proceeding.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/web-infra-dev) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
