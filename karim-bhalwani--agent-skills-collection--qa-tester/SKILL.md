---
name: qa-tester
description: Expert in automated QA testing, E2E test automation, visual regression testing, and intelligent test generation. Use when building automated test suites, validating user workflows, testing external integrations, setting up test infrastructure, or implementing AI-powered test discovery. Use when this capability is needed.
metadata:
  author: karim-bhalwani
---

# QA Tester Skill - Automated Testing & Test Automation

## Overview

The QA Tester skill specializes in building comprehensive, automated test suites that validate user workflows, external integrations, and application functionality. It bridges the gap between implementation and code review by ensuring functional correctness through intelligent, automated testing strategies inspired by modern AI-powered approaches like Abacus AI's DeepAgent.

This skill focuses on **user-facing test automation**, **E2E validation**, and **intelligent test generation**—complementing Guardian's code review focus and Implementer's feature development.

## When to Use This Skill

Use when:

- Building automated test suites for new features post-implementation
- Validating user workflows and landing pages (form filling, navigation, user interactions)
- Testing external integrations (email verification, payment gateways, third-party APIs)
- Setting up E2E test infrastructure in CI/CD pipelines
- Discovering edge cases and generating intelligent test cases
- Implementing visual regression testing for UI components
- Validating cross-browser and multi-device compatibility
- Creating reusable test fixtures and data management strategies
- Before code review (Guardian) to ensure functional correctness

---

## Core Capabilities

1. **E2E Test Automation**: Design and implement automated test suites using modern frameworks (Playwright, Cypress, Selenium) for user workflows and UI interactions
2. **Intelligent Test Generation**: Discover edge cases and auto-generate test cases using AI-powered approaches (DeepAgent-style testing)
3. **External Integration Testing**: Validate third-party services, APIs, email systems, payment gateways, and dependent systems
4. **Visual Regression Testing**: Detect unintended UI changes and layout regressions across components and pages
5. **Test Data & Fixtures**: Build robust test data management, fixtures, and factory patterns for test isolation and repeatability
6. **Test Infrastructure & CI/CD Integration**: Set up automated test execution, reporting, and defect tracking (Jira integration)
7. **Accessibility & Performance Testing**: Validate WCAG compliance, lighthouse scores, and user experience metrics
8. **Cross-Browser & Device Testing**: Ensure consistency across browsers, devices, and responsive layouts

---

## Workflow / Process

### Phase 1: Test Planning & Strategy

1. Review feature specifications and user workflows
2. Define test scope: unit coverage gaps, critical user paths, integration points
3. Identify external systems requiring validation (APIs, email, payments, etc.)
4. Choose appropriate testing levels: E2E, integration, visual regression
5. Plan test data strategy and environment setup

### Phase 2: Test Automation Implementation

1. Set up test framework and infrastructure (Playwright/Cypress/Selenium config)
2. Create base test fixtures and page object models for maintainability
3. Implement critical user journey tests (happy path + key error scenarios)
4. Build external integration tests (API mocking, email verification, third-party validation)
5. Implement visual regression baselines
6. Add accessibility checks (WCAG compliance)
7. Configure test data factories and cleanup strategies

### Phase 3: Intelligent Test Discovery

1. Analyze feature code for edge cases and boundary conditions
2. Use AI-powered test generation to identify untested paths
3. Create tests for common failure modes (network timeouts, invalid states, race conditions)
4. Implement property-based testing for complex logic
5. Document discovered edge cases and non-obvious test scenarios

### Phase 4: CI/CD Integration & Reporting

1. Configure automated test execution in CI/CD pipeline
2. Set up test result aggregation and reporting
3. Implement automated defect detection (screenshots, logs, video)
4. Integrate with issue tracking (Jira) for automatic bug filing
5. Configure performance baselines and regression monitoring
6. Create test coverage reports and quality dashboards

### Phase 5: Validation & Maintenance

1. Execute full test suite against feature
2. Validate cross-browser and device compatibility
3. Confirm email verification and external system integration
4. Create test documentation and runbooks
5. Establish test maintenance SLAs and update procedures

---

## Outputs & Deliverables

- **Primary Output**: Complete automated test suite with E2E tests, integration tests, visual regression baselines, and accessibility checks (organized in test directory structure)
- **Secondary Outputs**:
  - Test infrastructure configuration (CI/CD pipeline setup)
  - Test data fixtures and factory patterns
  - Test documentation and runbook
  - Defect reports with screenshots/logs (auto-filed to Jira)
  - Code coverage and test execution reports
- **Success Criteria**:
  - All critical user workflows covered by automated tests
  - External integrations validated with passing tests
  - Visual regression baselines established
  - Tests integrated into CI/CD and running on every commit
  - Zero flaky tests (deterministic, isolated, repeatable)
  - Test coverage >70% for E2E flows
- **Quality Gate**: All tests pass, external integrations verified, accessibility compliance confirmed, performance baselines met

---

## Standards & Best Practices

### E2E Test Design

- **Page Object Model**: Separate test logic from UI selectors for maintainability
- **Test Isolation**: Each test is independent, no shared state or order dependency
- **Deterministic Tests**: Control time, network, randomness; no flaky tests allowed
- **Meaningful Assertions**: Test business outcomes, not implementation details
- **Test Naming**: Clear, descriptive names that describe the user scenario (`should_complete_checkout_with_valid_card`)

### External Integration Testing

- **Mocking Strategy**: Mock external services in unit/integration tests; use real services in staging E2E
- **Error Scenarios**: Test timeout, rate limit, invalid response, and network failure handling
- **API Contract Testing**: Validate API response schemas against contracts
- **Email Verification**: Implement inbox mocking (Mailinator, mailtrap) or real email testing in staging
- **Payment Testing**: Use sandbox environments; never test with production credentials

### Intelligent Test Generation

- **DeepAgent-Style Discovery**: Analyze code paths to identify untested scenarios
- **Boundary Testing**: Test edge cases (empty strings, null, max values, min values)
- **Error Path Testing**: What happens when APIs fail, timeouts occur, or invalid data is provided
- **State Machine Testing**: Test all state transitions and invalid state combinations
- **Property-Based Testing**: Use hypothesis (Python) or fast-check (JS) for randomized testing

### Visual Regression Testing

- **Baseline Management**: Version control visual baselines; review and approve changes
- **Responsive Testing**: Capture at multiple breakpoints (mobile, tablet, desktop)
- **Component Focus**: Test individual components in isolation (Storybook integration)
- **Ignore Dynamic Content**: Use masks for timestamps, dynamic IDs, moving elements
- **Diff Thresholds**: Configure sensitivity; avoid false positives

### Test Data Management

- **Factory Pattern**: Use factories (FactoryBot, Faker) over fixtures for test data
- **Isolation**: Create data per test; clean up automatically
- **Realistic Data**: Use production-like data shapes; include edge cases
- **Seed Data**: Maintain consistent seed for deterministic tests
- **Performance**: Optimize data creation to keep test suites fast

### CI/CD Integration

- **Parallel Execution**: Run tests in parallel to reduce feedback time
- **Retry Strategy**: Configure smart retries for flaky external systems (exponential backoff)
- **Reporting**: Capture screenshots, videos, logs on failure
- **Blocking**: E2E test failures should block merge or production deployment
- **Performance Tracking**: Monitor test suite execution time trends
- **Defect Tracking**: Auto-file issues with logs, screenshots, environment details

### Accessibility & Performance

- **WCAG 2.1 AA**: Validate color contrast, keyboard navigation, screen reader compatibility
- **Lighthouse**: Monitor performance, SEO, and accessibility scores
- **Core Web Vitals**: Track LCP, FID, CLS metrics
- **User Experience**: Validate perceived performance; track real user metrics (RUM)

### Cross-Browser & Device Testing

- **Target Browsers**: Chrome, Firefox, Safari, Edge (based on analytics)
- **Mobile Testing**: iOS Safari, Chrome Mobile; test orientation changes
- **Responsive Design**: Verify layout integrity across breakpoints
- **Touch Interactions**: Test touch events and gesture handling
- **Accessibility**: Validate across browser zoom levels and high contrast modes

---

## Integration Points

| Phase | Input From | Output To | Context |
|-------|-----------|-----------|---------|
| Planning | `architect`, `implementer` | Test strategy document | Feature spec and implementation review |
| Automation | Implementation code | Test suite | Build automated tests as features complete |
| Integration | Test results | `guardian` review gate | Functional correctness confirmed |
| External Validation | External systems (APIs, email, payments) | Integration test results | Third-party system verification |
| CI/CD | Test suite | `github-workflow-automation` | Automated test execution pipeline |
| Reporting | Test execution | Issue tracking (Jira) | Auto-file defects with evidence |
| Approval | All tests passing | `verification-before-completion` | Feature ready for code review and deployment |

---

## Constraints

**Technical Constraints:**

- No performance optimization testing. Route to `guardian` for profiling and bottleneck analysis.
- No unit test writing. Implementer is responsible for unit tests; QA Tester handles E2E and integration.
- Cannot test functionality that doesn't exist. Must work with implementer to build testable code first.
- External services require sandbox/staging access. Cannot test against production systems except in extraordinary cases.

**Scope Constraints:**

- **In Scope**: E2E workflows, user interactions, external integrations, visual regression, accessibility, cross-browser validation, intelligent test generation, test infrastructure
- **Out of Scope**: Unit testing (implementer), code review (guardian), security scanning (top-web-vulnerabilities), performance profiling (guardian), infrastructure deployment (ops-manager)

---

## Common Pitfalls

- **Over-Testing**: Testing implementation details instead of user outcomes. Focus on "what the user does," not "how the code works."
- **Flaky Tests**: Tests that pass sometimes and fail sometimes are useless. Control time, network, randomness; isolate tests; use proper waits, not sleeps.
- **No Test Data Strategy**: Hard-coded test data causes brittle tests. Use factories, fixtures, and cleanup routines; make tests data-independent.
- **Ignoring External Systems**: Mocking all APIs prevents discovering real integration bugs. Use staging with real (or sandbox) external services for E2E tests.
- **Slow Test Suites**: Tests that take hours to run won't be used. Parallelize, optimize data creation, mock heavy operations; aim for <10 min full suite.
- **Missing Error Scenarios**: Testing only the happy path misses 80% of real-world bugs. Test timeouts, invalid responses, retries, rate limits, and network failures.
- **No Visual Baseline Management**: Visual regression without version-controlled baselines causes approval fatigue. Review, approve, version-control all visual changes.
- **Ignoring Accessibility**: Testing only sighted keyboard users misses screen reader issues. Use axe-core, test with screen readers, validate WCAG compliance.
- **Not Using Page Objects**: Scatter UI selectors throughout tests makes refactoring nightmare. Centralize selectors in Page Object Model; separate test logic from UI.
- **Silent Failures**: Tests that "pass" but don't actually validate anything. Ensure assertions are meaningful and failures are obvious.

---

## Dependencies

- **Depends On**: `implementer` (code to test), `architect` (feature specs)
- **Related Skills**: `guardian` (code review), `top-web-vulnerabilities` (security testing), `verification-before-completion` (work validation), `github-workflow-automation` (CI/CD integration)

---

## Reference Files

Detailed guides for test automation techniques and patterns are available in the `references/` folder.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/karim-bhalwani) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
