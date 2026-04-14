---
name: writing-playwright-tests
description: Use when writing new Playwright E2E tests or adding test cases. Provides testing philosophy, patterns, and best practices from the Playwright Developer Handbook.
metadata:
  author: open-metadata
---

# Writing Playwright Tests Skill

This skill guides you through writing Playwright E2E tests following OpenMetadata standards.

**Reference**: @openmetadata-ui/src/main/resources/ui/playwright/PLAYWRIGHT_DEVELOPER_HANDBOOK.md

## ESLint Enforcement

All Playwright tests are linted with `eslint-plugin-playwright`. Run before submitting:

```bash
cd openmetadata-ui/src/main/resources/ui
yarn lint:playwright
```

**Error-level rules block CI**: `no-networkidle`, `no-page-pause`, `no-focused-test`.
**Warning-level rules** highlight anti-patterns to fix: `missing-playwright-await`, `no-wait-for-timeout`, `no-force-option`, `no-element-handle`, `prefer-web-first-assertions`, and more. See the handbook's **ESLint Enforcement** section for details.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/open-metadata) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
