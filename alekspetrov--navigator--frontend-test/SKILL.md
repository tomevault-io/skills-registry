---
name: frontend-test
description: Generate frontend component tests (unit, snapshot, e2e). Auto-invoke when user says "test this component", "write component test", or "add component test". Use when this capability is needed.
metadata:
  author: alekspetrov
---

# Frontend Test Generator

Generate React/Vue component tests with React Testing Library including user interactions.

## When to Invoke

Auto-invoke when user mentions:
- "Test this component"
- "Write component test"
- "Test component"
- "Add component test"
- "Component tests for [name]"

## What This Does

1. Generates test file with RTL utilities
2. Tests component rendering
3. Tests user interactions (click, type, etc.)
4. Tests accessibility
5. Generates snapshot tests

## Success Criteria

- [ ] Test file generated with RTL imports
- [ ] Tests render component correctly
- [ ] User interactions are tested
- [ ] Accessibility attributes validated
- [ ] Tests follow React Testing Library best practices

**Auto-invoke when writing frontend component tests** ⚛️

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/alekspetrov) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
