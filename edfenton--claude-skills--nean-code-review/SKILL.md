---
name: nean-code-review
description: Review NEAN code for compliance with standards, NFRs, and security policy. Use when this capability is needed.
metadata:
  author: edfenton
---

## Purpose

Review code against nean-std, nean-nfr, and nean-sec policies. Report issues, then (with approval) fix and run tests to confirm.

## Arguments

- `--paths <glob>` — Limit review scope (default: whole repo)
- `--no-fix` — Report only, don't offer to fix

## Workflow

### 1. Automated gates

```bash
npm run lint
npm run format -- --check
npx nx affected --target=typecheck
```

### 2. Policy review

Review against:

- **nean-std** — coding standards, project structure, conventions
- **nean-nfr** — performance, reliability, observability, accessibility
- **nean-sec** — input validation, SQL injection prevention, auth, error handling

For each issue, note:

- Category: `std` | `nfr` | `sec`
- Severity: `must-fix` | `should-fix` | `nice-to-have`
- File and location
- What's wrong and how to fix it

### 3. Report results

Summary of automated gate results + policy findings grouped by severity.

### 4–5. Approval gate, fix and confirm

See `/shared-review-workflow` for severity definitions, approval gate protocol, and fix constraints. Run `/nean-unit-test` to confirm no regressions after fixes.

## Common issues to check

### Security (nean-sec)
- [ ] Raw SQL queries instead of TypeORM query builder
- [ ] Missing ValidationPipe on controller methods
- [ ] DTOs without class-validator decorators
- [ ] Missing guards on protected endpoints
- [ ] Sensitive data in error responses
- [ ] Missing rate limiting on auth endpoints

### Standards (nean-std)
- [ ] Business logic in controllers (should be in services)
- [ ] Missing OpenAPI decorators
- [ ] Incorrect file/folder naming
- [ ] Missing barrel exports
- [ ] Entities exposed directly (should use DTOs)

### NFRs (nean-nfr)
- [ ] Missing pagination on list endpoints
- [ ] N+1 query patterns
- [ ] Missing indexes on frequently queried columns
- [ ] OnPush not used in Angular components
- [ ] Missing error handling in effects

## Reference

For review checklists and common issues, see `reference/nean-code-review-reference.md`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/edfenton) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
