---
name: flutter-testing
description: Flutter testing skill hub: unit/widget/integration tests, mocking, golden tests, and CI stability patterns. Use when this capability is needed.
metadata:
  author: melsaeed276
---

# Testing Skills

## What this domain covers

Testing strategy and patterns for unit, widget, and integration tests; mocking; golden tests; and CI stability.

## When to read it

- Bugs keep coming back (need regression tests).
- UI changes accidentally break layouts at different sizes.
- CI is flaky or slow.

## Subtopics

- Unit tests: [unit_tests.md](./unit_tests.md)
- Widget tests: [widget_tests.md](./widget_tests.md)
- Integration tests: [integration_tests.md](./integration_tests.md)
- Mocking: [mocking.md](./mocking.md)
- Golden tests: [golden_tests.md](./golden_tests.md)
- CI testing: [ci_testing.md](./ci_testing.md)

## Decision guide

- If you need **fast logic confidence**, go to [unit_tests.md](./unit_tests.md).
- If you need **UI behavior and layout confidence**, go to [widget_tests.md](./widget_tests.md) and [golden_tests.md](./golden_tests.md).
- If you need **end-to-end confidence**, go to [integration_tests.md](./integration_tests.md).
- If you need **stable pipelines**, go to [ci_testing.md](./ci_testing.md).

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/melsaeed276) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
