---
name: testing-guide
description: Testing strategy for backend services (unit, integration, and contract checks). Keywords: testing, unit tests, integration tests, test strategy, mocking. Use when this capability is needed.
metadata:
  author: willyu1007
---

# Testing Guide

This skill provides a pragmatic testing strategy for backend services.

---

## 1. Principles

- Prefer **fast unit tests** for business logic (service layer).
- Use **integration tests** for routes and repositories when needed.
- Keep tests deterministic (fixed inputs, stable fixtures, clear assertions).

---

## 2. What to test by layer

### Routes/controllers

- HTTP status codes and response shape
- Input validation behavior (invalid input → 400)
- Auth/permission behavior (missing/invalid auth → 401/403)

### Services

- Business rules and edge cases
- Idempotency and invariants
- Error conditions (typed domain errors)

### Repositories

- Query correctness and filtering/pagination behavior
- Transactional behavior (multi-write operations)

---

## 3. Common anti-patterns

- Tests that depend on real clocks (use time injection or fakes)
- Tests that require external services without a stable harness
- "Golden" snapshots for dynamic responses without normalization

---

## 4. Checklist (before merging changes)

- [ ] Service logic covered by unit tests
- [ ] At least one integration test covers the route(s) you changed
- [ ] Failure paths are tested (validation/auth/not found)
- [ ] Monitoring/error capture behavior is testable where possible

---

## Related Skills

- `architecture-overview`
- `services-and-repositories`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/willyu1007) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
