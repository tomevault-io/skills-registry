---
name: vkc-e2e-playwright-runbook
description: Run and debug Playwright E2E in VKC (Playwright, E2E_PORT=3100, E2E_HEALTH_PATH, webServer, test-results/*/error-context.md). Use when adding/changing e2e specs or debugging E2E failures. (키워드= E2E, 플레이라이트, 테스트, 포트 3100, 헬스체크, webServer, 플래키) Use when this capability is needed.
metadata:
  author: lee-sang-bok
---

# VKC E2E Playwright Runbook

## When to use

- You add or edit `e2e/*.spec.ts`.
- You debug flaky E2E failures locally or in CI.
- You see port/health-check issues (`E2E_PORT`, `E2E_HEALTH_PATH`, `webServer`).

## Hard rules (this repo)

- Default E2E port is **3100** (not 3000) unless explicitly overridden.
- Prefer the project runner: `scripts/run-playwright-e2e.js` via `npm run test:e2e`.
- Always capture failure context:
  - `test-results/*/error-context.md`

## Canonical references

- Runner: `scripts/run-playwright-e2e.js`
- Scripts: `package.json` (`test:e2e*`)
- E2E non-negotiables: `AGENTS.md`

## References

- Runbook: `.codex/skills/vkc-e2e-playwright-runbook/references/e2e-runbook.md`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/lee-sang-bok) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
