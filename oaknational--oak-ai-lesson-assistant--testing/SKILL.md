---
name: testing
description: Testing commands and patterns for Oak AI project including running Jest unit tests, E2E tests with Playwright, coverage reports, and filtering tests. Use when writing tests, running test suites, or debugging test failures. Use when this capability is needed.
metadata:
  author: oaknational
---

# Testing

## Running Tests

### All Tests

```bash
pnpm test
```

### Specific Test Pattern

```bash
pnpm test -- -t "test name pattern"
```

### E2E Tests

```bash
pnpm test-e2e
```

### Coverage Report

```bash
pnpm test-coverage
```

## Package-Specific Tests

Run tests for specific packages:

```bash
pnpm --filter @oakai/aila test
pnpm --filter @oakai/nextjs test
pnpm --filter @oakai/api test
```

## Test Framework

- **Unit Tests**: Jest
- **Component Tests**: React Testing Library
- **E2E Tests**: Playwright

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/oaknational) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
