---
name: validate
description: Run full validation suite (build, lint, type check, tests) Use when this capability is needed.
metadata:
  author: andrewmclachlan
---

# Validate

Run full validation suite on the codebase. Execute in order, stopping on first failure.

## Steps

### 1. Backend Build

```bash
dotnet build MooBank.slnx --no-restore
```

### 2. Frontend Lint

```bash
cd src/MooBank.Web.App && npm run lint
```

### 3. Frontend Build (includes type checking)

```bash
cd src/MooBank.Web.App && npm run build
```

### 4. Backend Tests

```bash
dotnet test tests/ --no-build
```

## Summary Report

After all validations complete, provide:

- Backend build: PASS/FAIL
- Frontend lint: PASS/FAIL
- Frontend build: PASS/FAIL
- Backend tests: PASS/FAIL (X passed, Y failed)
- Any warnings encountered
- **Overall: PASS/FAIL**

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/andrewmclachlan) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
