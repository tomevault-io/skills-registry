---
name: webapp-testing
description: Quality assurance standards for web applications. Use when writing unit tests, integration tests, or checking for bugs. Use when this capability is needed.
metadata:
  author: wynautbhav
---

# Webapp Testing Skill

## Testing Pyramid
1. **Unit Tests (Most)**: Test individual functions (e.g., "Does this calculate the total correctly?").
2. **Integration Tests (Some)**: Test how components work together (e.g., "Does the form submit data to the API?").
3. **E2E Tests (Few)**: Test critical user flows (e.g., "Can a user log in and buy an item?").

## Best Practices
- **Test Behavior, Not Implementation**: Test *what* the component renders, not *how* its state is managed.
- **Mock External Calls**: Never call real APIs in unit tests. Use mocks.
- **Green-Red-Refactor**: Write the test, watch it fail, write the code, watch it pass.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/wynautbhav) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
