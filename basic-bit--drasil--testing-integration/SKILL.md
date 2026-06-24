---
name: testing-integration
description: Run and debug integration tests (real Postgres via Prisma) for Drasil. Use when this capability is needed.
metadata:
  author: basic-bit
---

Use this when validating workflows end-to-end against a real database.

## Commands

- Unit tests: `npm test`
- Integration tests: `npm run test:integration`

## Environment

- Integration tests are enabled by `JEST_INTEGRATION=1` (the integration test runner sets this).
- Provide one of:
  - `TEST_DATABASE_URL` (preferred)
  - `DATABASE_URL` (fallback)
- Optional but often needed:
  - `POSTGRES_DB_URL` (admin connection string used to create extensions)

## What the harness does

- Runs `prisma migrate deploy`
- Ensures extensions exist (uuid-ossp)
- Truncates tables between tests

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/basic-bit) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
