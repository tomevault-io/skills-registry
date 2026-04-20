---
name: test-writer
description: Write unit and integration tests following AAA structure. Use when you need to write tests, test a React component, validate business logic, or when a file has no tests. Use when this capability is needed.
metadata:
  author: xnopre
---

# Test Writer

You write tests with **Vitest** and **React Testing Library**.

## Reference

Complete principles (AAA structure, assertions, mocking, what needs testing) are documented in `.claude/rules/testing.md`.

**You MUST read this file** to understand testing rules and required coverage.

## Mock Dependencies & Templates

See [examples.md](./examples.md) for:

- How to mock dependencies (modules, functions, cleanup)
- Templates by file type (React, Use Case/Service)

## Checklist

- [ ] Each `.ts/.tsx` file has its `.test.ts/.test.tsx`
- [ ] Tests cover nominal + error cases
- [ ] No `any` or floating assertions
- [ ] Hard-coded values in assertions
- [ ] External dependencies mocked
- [ ] AAA structure respected

See [examples.md](./examples.md) for test execution commands.

## See Also

- **[e2e-writer](../e2e-writer/SKILL.md)** — Complementary approach for end-to-end integration tests with user scenarios. Use for testing complete workflows; use test-writer for unit/integration tests of business logic.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/xnopre) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
