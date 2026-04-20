---
name: test
description: Run and troubleshoot tests for this repository (Go API/consumer) using the containerized Go toolchain. Use when the user asks to run tests, verify a change, investigate a failing test, or respond to CI failures. Covers unit tests and the optional integration concurrency test. Use when this capability is needed.
metadata:
  author: entazis
---

# Test

## Quick start

Prefer the repository’s containerized Go toolchain so local Go versions don’t matter.

### Unit tests (default)

Run all unit tests:

```bash
docker run --rm -v "$PWD":/src -w /src golang:1.25.6-alpine sh -c "go test ./..."
```

If you only changed a specific area, prefer a narrower command first (faster), e.g.:

```bash
docker run --rm -v "$PWD":/src -w /src golang:1.25.6-alpine sh -c "go test ./internal/..."
```

### Integration test (optional; requires Docker)

Run the concurrency integration test (testcontainers):

```bash
RUN_INTEGRATION=1 docker run --rm -v "$PWD":/src -w /src golang:1.25.6-alpine sh -c "go test ./... -run TestConcurrentReviewCreates_UpdateAggregates"
```

## When tests fail (workflow)

1. Re-run the smallest reproducer:
   - If only one package fails, re-run `go test` for that package.
   - If a single test fails, re-run with `-run <TestName>`.
2. Read the failure output and identify:
   - exact failing test name(s)
   - expected vs actual
   - whether the failure is deterministic or flaky
3. Fix the root cause (not the symptom), then re-run:
   - the failing test(s)
   - the broader package
   - finally `go test ./...` (unit) and, if relevant, the integration test above

## Notes specific to this repo

- Use `docker compose up -d --build` to start Postgres/Redis/RabbitMQ when you need runtime dependencies, but unit tests should generally not require it.
- Integration tests requiring Docker should be run only when relevant to the change or when explicitly requested.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/entazis) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
