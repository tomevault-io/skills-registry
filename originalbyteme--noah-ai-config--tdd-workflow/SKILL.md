---
name: tdd-workflow
description: Test-Driven Development workflow with RED-GREEN-REFACTOR cycle Use when this capability is needed.
metadata:
  author: originalbyteme
---

# TDD Workflow Skill

Follow the Test-Driven Development cycle for implementing features.

## Process

### 1. RED - Write Failing Test
- Write a test for the desired behavior
- Run the test to confirm it fails
- Ensure the failure is for the right reason

### 2. GREEN - Make It Pass
- Write the minimal code to pass the test
- Don't over-engineer or add extra features
- Focus only on passing the current test

### 3. REFACTOR - Improve
- Clean up the implementation
- Remove duplication
- Improve naming and structure
- Ensure tests still pass

## Guidelines

- One test at a time
- Small, incremental steps
- Tests should be fast and isolated
- Test behavior, not implementation details
- Aim for ~80% meaningful coverage

## Verification

After each cycle:
- [ ] New test written and initially failing
- [ ] Implementation passes the test
- [ ] Code refactored for clarity
- [ ] All existing tests still pass

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/originalbyteme) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
