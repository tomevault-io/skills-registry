---
name: testing
description: Comprehensive testing across platforms — Web (Playwright, Vitest), Backend (Rust/Go/Python/Node API testing), Desktop (Tauri, Electron), Mobile (React Native, Flutter - coming soon). Unit, integration, E2E, load, security, accessibility testing. Use for test automation, quality assurance, CI/CD integration. Use when this capability is needed.
metadata:
  author: thienty1207
---

# Testing Mastery

Platform-agnostic testing strategy covering Web, Backend, Desktop, and Mobile applications. Core principles apply across all platforms.

> **Note:** Desktop (Tauri/Electron) and Mobile (React Native/Flutter) testing guides coming soon.

## Platform Coverage

| Platform | Status | Frameworks |
|----------|--------|------------|
| **Web Frontend** | ✅ Complete | Vitest, Playwright, React Testing Library |
| **Web Backend** | ✅ Complete | Rust (cargo test), Go, Python (pytest), Node |
| **Desktop** | 🚧 Coming | Tauri, Electron (WebDriver) |
| **Mobile** | 🚧 Coming | React Native (Detox), Flutter |

## Testing Pyramid (70-20-10)

| Layer | Ratio | Framework | Speed | What to Test |
|-------|-------|-----------|-------|-------------|
| Unit | 70% | Vitest | <50ms | Functions, utils, state logic |
| Integration | 20% | Vitest + MSW | 100-500ms | API endpoints, DB ops, modules |
| E2E | 10% | Playwright | 5-30s | Critical flows (login, checkout) |

## Quick Start

```bash
# Unit tests
npx vitest run                           # Run all
npx vitest run --coverage                # With coverage
npx vitest --ui                          # Visual UI

# E2E tests
npx playwright test                      # Run all
npx playwright test --ui                 # Interactive UI
npx playwright test --headed             # See browser
npx playwright codegen https://myapp.com # Record test

# Load test
k6 run load-test.js

# Accessibility
npx @axe-core/cli https://myapp.com
npx lighthouse https://myapp.com --output=html
```

## Reference Navigation

### Frontend Testing
- **[Unit & Integration Testing](references/unit-integration-testing.md)** — Vitest patterns, mocking, MSW, test organization
- **[E2E Testing with Playwright](references/e2e-playwright.md)** — Page objects, selectors, assertions, parallelism, debugging
- **[Component Testing](references/component-testing.md)** — React Testing Library, render patterns, user events

### Backend Testing (Multi-stack)
- **[Backend Unit Testing](references/backend-unit-testing.md)** — Rust (cargo test), Go, Python (pytest), Node (Jest/Vitest)
- **[API Integration Testing](references/api-integration-testing.md)** — HTTP endpoint testing, database integration, test containers
- **[Database Testing](references/database-testing.md)** — Migration tests, fixtures, transaction rollback patterns

### Performance & Load
- **[Load Testing with k6](references/load-testing-k6.md)** — Scenarios, thresholds, custom metrics, CI integration
- **[Performance Testing](references/performance-testing.md)** — Core Web Vitals, Lighthouse CI, bundle analysis
- **[API Load Testing](references/api-load-testing.md)** — Backend stress testing, concurrency, benchmarking

### Security & Accessibility
- **[Security Testing](references/security-testing.md)** — OWASP Top 10, SQL injection, XSS, CSRF, auth bypasses
- **[Accessibility Testing](references/accessibility-testing.md)** — WCAG 2.1, axe-core, keyboard nav, screen readers

### Quality & Strategy
- **[Visual Regression](references/visual-regression.md)** — Screenshot comparison, responsive snapshots, CI workflow
- **[Test Strategy Guide](references/test-strategy-guide.md)** — What to test, flakiness mitigation, test data, CI/CD gates
- **[Cross-Browser & Mobile](references/cross-browser-mobile.md)** — Browser matrix, device testing, responsive verification

## CI/CD Integration

### Frontend Pipeline
```yaml
jobs:
  test-frontend:
    steps:
      - run: npm run test:unit        # Gate 1: Fast fail (<30s)
      - run: npm run test:integration # Gate 2: API mocking
      - run: npm run test:e2e         # Gate 3: Critical flows
      - run: npm run test:a11y        # Gate 4: Accessibility
      - run: npx lhci autorun         # Gate 5: Performance
```

### Backend Pipeline (Rust)
```yaml
jobs:
  test-backend:
    steps:
      - run: cargo test --lib         # Unit tests
      - run: cargo test --test '*'    # Integration tests
      - run: cargo clippy             # Linting
```

### Backend Pipeline (Multi-stack)
```yaml
# Go
- run: go test ./...
- run: golangci-lint run

# Python
- run: pytest tests/
- run: ruff check .

# Node.js
- run: npm run test
- run: npm run lint
```

## Best Practices

1. **Test behavior, not implementation** — Test what the user sees, not internal state
2. **Arrange-Act-Assert** — Clear structure for every test
3. **Avoid test interdependence** — Each test runs in isolation
4. **Use factories, not fixtures** — Generate fresh test data
5. **Flaky tests = bugs** — Fix or quarantine immediately
6. **Coverage ≠ Quality** — 80% meaningful > 100% shallow

## Related Skills

| Skill | When to Use |
|-------|-------------|
| [rust-backend-advance](../rust-backend-advance/SKILL.md) | Rust-specific testing patterns with cargo test |
| [nextjs-turborepo](../nextjs-turborepo/SKILL.md) | Next.js E2E testing setup |
| [databases](../databases/SKILL.md) | Database testing, migrations |
| [devops](../devops/SKILL.md) | CI/CD pipeline test integration |
| [debugging](../debugging/SKILL.md) | Test failure investigation |
| [security](../security/SKILL.md) | Security testing methodologies |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/thienty1207) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
