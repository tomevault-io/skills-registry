---
name: expense-analyser-test-builder
description: Set up and build automated tests for the expense-analyser Vue project using the best-fit framework for its stack. Use when adding a test suite from scratch or expanding coverage for src/components, src/stores, src/services, src/router, and TypeScript models in this Vite + Vue 3 codebase. Use when this capability is needed.
metadata:
  author: rnavdeep
---

# Expense Analyser Test Builder

Use `Vitest + @vue/test-utils + jsdom` as the default testing framework for this project.

Read `references/project-testing-strategy.md` before implementation.

Use templates from `assets/` for setup and first tests.

## Workflow

1. Install test tooling.
- Add dev dependencies: `vitest`, `@vitest/coverage-v8`, `@vue/test-utils`, `jsdom`.
- Keep `npm` as package manager to match existing lockfile.

2. Add test config and scripts.
- Extend `vite.config.ts` with a `test` block or create `vitest.config.ts`.
- Add scripts in `package.json`:
  - `test`: `vitest run`
  - `test:watch`: `vitest`
  - `test:coverage`: `vitest run --coverage`
- Add setup file (`src/tests/setup.ts`) and include it in Vitest config.

3. Establish folder layout.
- Component tests: `src/components/__tests__/*.spec.ts`
- Store tests: `src/stores/**/__tests__/*.spec.ts`
- Service tests: `src/services/__tests__/*.spec.ts`
- Shared test utils: `src/tests/*`

4. Add first coverage in this order.
- Pure utilities/models with no Vue runtime dependency.
- Stores with mocked services.
- Components with store and router mocks.
- Service wrappers with mocked axios behavior.

5. Handle known project side effects.
- Mock `TextractNotificationService.start/stop` in tests that touch auth flow.
- Mock `useNotificationStore` when testing `useAuthStore.login`.
- Stub router pushes and route guards instead of running full navigation for unit tests.

6. Validate in CI-style order.
- Run `npm run test`.
- Run `npm run test:coverage`.
- Run `npm run type-check`.
- Run `npm run lint`.

## Coverage Targets

Prioritize high-risk paths:
- Auth session transitions (`login`, `checkSession`, `logout`)
- Expense create/list/delete flows in store actions
- Notification rendering branches (friend request vs normal message)
- Error handling paths in services (`axios.isAxiosError` branches)

## Guardrails

- Do not call real backend APIs in unit tests.
- Do not rely on shared mutable store state across tests; reset Pinia and mocks per test.
- Keep tests deterministic; avoid sleeps/timeouts unless testing retry/debounce logic.
- Add tests near the domain they cover to keep maintenance cost low.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rnavdeep) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
