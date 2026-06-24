---
name: logics-acceptance-to-tests
description: Convert acceptance criteria into a concrete validation/test plan. Use when Codex should turn backlog/spec acceptance criteria into unit/integration/e2e test ideas and update the task/spec validation section with relevant commands. Use when this capability is needed.
metadata:
  author: alexago83
---

# Acceptance → Tests

## Do

- Rewrite each acceptance criterion as a verifiable check.
- Map checks to test types:
  - Unit: pure logic and edge cases
  - Integration: components + state + API boundaries
  - E2E: user flows and regressions
- Update the `# Validation` section in tasks with the most relevant commands:
  - `npm run lint`
  - `npm run tests`
  - `npm run typecheck`
  - `npm run build`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/alexago83) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
