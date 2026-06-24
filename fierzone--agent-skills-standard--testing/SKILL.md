---
name: testing
description: Standards for unit testing, table-driven tests, and mocking in Golang. Use when this capability is needed.
metadata:
  author: fierzone
---

# Golang Testing Standards

## **Priority: P0 (CRITICAL)**

## Principles

- **Table-Driven Tests**: The idiomatic way to write tests in Go.
- **Subtests (`t.Run`)**: Run table entries as subtests for better reporting.
- **Parallel Usage**: Use `t.Parallel()` for independent tests to speed up execution.
- **Mock Interfaces**: Mock at the boundaries (interfaces).

## Tools

- **Stdlib**: `testing` package is usually enough.
- **Testify (`stretchr/testify`)**: Assertions (`assert`, `require`) and Mocks.
- **Mockery**: Auto-generate mocks for interfaces.
- **GoMock**: Another popular mocking framework.

## Naming

- Test file: `*_test.go`
- Test function: `func TestName(t *testing.T)`
- Example function: `func ExampleName()`

## Anti-Patterns

- **Sleeping in tests**: Use channels/waitgroups or retry logic.
- **Testing implementation details**: Test public behavior/interface.

## References

- [Table-Driven Tests](references/table-driven-tests.md)
- [Mocking Strategies](references/mocking-strategies.md)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/fierzone) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
