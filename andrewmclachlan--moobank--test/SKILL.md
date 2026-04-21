---
name: test
description: Run the test suite Use when this capability is needed.
metadata:
  author: andrewmclachlan
---

# Run Tests

## All Tests

```bash
dotnet test tests/
```

## Filtered

```bash
# Unit tests only
dotnet test --filter /[Category=Unit]

# Integration tests only
dotnet test --filter /[Category=Integration]

# Specific test name
dotnet test tests/ --filter "FullyQualifiedName~YourTestName"
```

## Frontend Tests

```bash
cd src/MooBank.Web.App && npm test
```

## Report

- Total tests run
- Passed / Failed / Skipped
- Failed test details with error messages

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/andrewmclachlan) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
