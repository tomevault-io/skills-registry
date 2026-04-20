---
name: write-e2e-tests
description: When you need to write BDD (behaviour driven development) style e2e tests for Tracker Use when this capability is needed.
metadata:
  author: eighttrigrams
---

Tests are run with `make e2e` or `make e2e-docker` (to verify once they have been verified and developed outside the container).

E2E tests are maintained inside the `e2e` directory.

Configurations are `Dockerfile.e2e` and `playwright.config.ts`. Both will run the tests with an in memory database,
and therefore "start fresh" every time.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/eighttrigrams) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
