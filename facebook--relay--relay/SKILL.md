---
name: relay-e2e-test
description: Write and run markdown-driven e2e tests for Relay. Covers fixture format, server/client code patterns, interaction DSL, snapshots, and running tests. Use when this capability is needed.
metadata:
  author: facebook
---

# Relay E2E Tests

Markdown-driven end-to-end tests for Relay. Each test is a self-contained `.md` file that defines a GraphQL server (via [Grats](https://grats.capt.dev/)), a Relay-powered React component, and optional interaction steps. The test harness extracts the code blocks, compiles them with Grats + relay-compiler, renders with React Testing Library, runs interactions, and snapshot-tests the output.

Tests run against **Relay runtime packages from source**, so changes are reflected immediately without a build step.

## References

Read the appropriate reference file based on your task:

- **[Writing fixtures](references/writing-fixtures.md)** — fixture format, example, server/client code patterns, interaction DSL, snapshots. Read when creating or modifying test fixtures.
- **[Running tests (internal)](references/running-tests-internal.md)** — setup, commands, and compiler resolution for fbsource / OD. Read when running tests internally.
- **[Running tests (GitHub)](references/running-tests-github.md)** — setup, commands, and compiler resolution for the OSS repo. Read when running tests on GitHub.

---
> Source: [facebook/relay](https://github.com/facebook/relay) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-24 -->
