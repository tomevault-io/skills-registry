---
name: testing
description: Comprehensive testing patterns and anti-patterns for writing and reviewing tests Use when this capability is needed.
metadata:
  author: vm0-ai
---

# Testing Skill

Use this skill when writing tests, reviewing test code, or investigating test failures.

## Documentation

Read the testing guide and relevant reference based on context:

| Context | Primary | Reference |
|---------|---------|-----------|
| General | [docs/testing.md](../../../docs/testing.md) | — |
| Anti-patterns | [docs/testing.md](../../../docs/testing.md) | [anti-patterns.md](../../../docs/testing/anti-patterns.md) |
| Patterns | [docs/testing.md](../../../docs/testing.md) | [patterns.md](../../../docs/testing/patterns.md) |
| CLI (`turbo/apps/cli`) | [docs/testing.md](../../../docs/testing.md) | [cli-testing.md](../../../docs/testing/cli-testing.md) |
| CLI E2E (`e2e/tests/`) | [docs/testing.md](../../../docs/testing.md) | [cli-e2e-testing.md](../../../docs/testing/cli-e2e-testing.md) |
| Web (`turbo/apps/web`) | [docs/testing.md](../../../docs/testing.md) | [web-testing.md](../../../docs/testing/web-testing.md) |
| App (`turbo/apps/platform`) | [docs/testing.md](../../../docs/testing.md) | [app-testing.md](../../../docs/testing/app-testing.md) |
| Rust (`crates/`) | [docs/testing.md](../../../docs/testing.md) | [rust-testing.md](../../../docs/testing/rust-testing.md) |
| Python addon (`crates/runner/mitm-addon`) | [docs/testing.md](../../../docs/testing.md) | [mitm-addon-testing.md](../../../docs/testing/mitm-addon-testing.md) |

## Key Principles

1. **Integration tests are primary** — test at system entry points
2. **Mock at the boundary** — only mock external services, not internal code
3. **Use real infrastructure** — real database, real filesystem (temp dirs)
4. **Test behavior, not implementation** — verify outcomes, not mock calls

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/vm0-ai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
