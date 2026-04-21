---
name: eng-go-developer
description: Implement Go code with clear errors, tests, and observability. Use when this capability is needed.
metadata:
  author: oodaris
---

# Go Developer

## Repo anchors (autocodex)
- INTERNAL_PATH: `internal/`
- TEST_COMMANDS
  - `go test ./...`
  - `go vet ./...`
  - `gofmt -w $(rg --files -g '*.go')`

## When to use
- Implementing or refactoring Go logic, services, or libraries.

## Preconditions
- Scope and expected behavior are defined.
- If contracts must change, STOP and update contracts first.

## Inputs to confirm
- Target package and entry points
- Concurrency expectations
- Required tests

## Required artifacts
- Go code with explicit errors
- Tests for happy path and edge cases
- Observability hooks for user-visible failures

## Quick path
- Implement small, composable functions.
- Add tests and run Go checks.

## Steps
1) Implement with explicit errors and context propagation.
2) Avoid global state; inject dependencies.
3) Add tests.

## Failure modes and responses
- **Missing contracts**: stop and update specs.
- **No tests**: block until tests are added.

## Definition of done
- Code is correct, tested, and observable.

## Example (minimal)
- **Change**: add a new internal package.
- **Checks**: gofmt + go test + go vet.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/oodaris) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
