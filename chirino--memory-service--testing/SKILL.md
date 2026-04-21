---
name: testing
description: Use when writing or debugging tests for memory-service. Covers Cucumber BDD patterns and failure reporting.
metadata:
  author: chirino
---

# Testing Guidelines

## Prefer Cucumber for API Testing
For REST and gRPC tests, use the Go BDD suite under `internal/bdd/testdata/features*/` instead of unit tests with mocks.

Reserve unit tests with mocks for internal implementation details and infrastructure testing.

## Cucumber Failure Reporting
When tests fail:
```bash
go test ./internal/bdd -run TestFeatures... > test.log 2>&1
rg -n "FAIL|ERROR|panic|--- FAIL:" test.log
```

Prefer searching the redirected log for the failing scenario and stack trace context.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/chirino) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
