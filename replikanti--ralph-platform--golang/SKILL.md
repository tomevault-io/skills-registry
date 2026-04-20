---
name: golang
description: Best practices and patterns for Go development Use when this capability is needed.
metadata:
  author: replikanti
---

# Go Development Guide

## Code Style
- Follow Effective Go guidelines
- Use goimports for formatting and import management
- Keep functions short and focused

## Common Patterns
- Accept interfaces, return structs
- Use table-driven tests
- Handle errors explicitly, don't ignore them
- Use context.Context for cancellation and timeouts

## Package Organization
- Keep package names short and lowercase
- Avoid package-level state when possible
- Use internal/ for private packages

## Error Handling
- Return errors, don't panic (except in truly unrecoverable situations)
- Wrap errors with context: `fmt.Errorf("failed to connect: %w", err)`
- Use sentinel errors for expected conditions

## Testing
- Place tests in same package (white-box) or _test package (black-box)
- Use testify/assert for cleaner assertions
- Use t.Parallel() for independent tests

## staticcheck
- Address all linter warnings
- Common issues: ineffassign, errcheck, staticcheck

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/replikanti) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
