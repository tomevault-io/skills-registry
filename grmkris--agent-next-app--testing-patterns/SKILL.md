---
name: testing-patterns
description: Testing patterns using bun:test with in-memory SQLite. Use when writing unit tests, integration tests, or router tests. Use when this capability is needed.
metadata:
  author: grmkris
---

# Testing Patterns

Use these patterns for writing tests with bun:test and in-memory SQLite.

## When to Use

- Writing unit tests for business logic
- Testing ORPC router handlers
- Integration testing with database

## Key Files

- `src/test/setup.ts` - Test infrastructure (createTestSetup, createTestContext)
- `src/test/helpers.ts` - Test helper functions

## Pattern Files

- [setup.md](setup.md) - Test setup and infrastructure
- [helpers.md](helpers.md) - Test helper patterns

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/grmkris) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
