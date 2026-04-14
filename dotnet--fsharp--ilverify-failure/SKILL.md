---
name: ilverify-failure
description: Fix ILVerify baseline failures when IL shape changes (codegen, new types, method signatures). Use when CI fails on ILVerify job. Use when this capability is needed.
metadata:
  author: dotnet
---

# ILVerify Baseline

## When to Use
IL shape changed (codegen, new types, method signatures) and ILVerify CI job fails.

## Update Baselines
```bash
TEST_UPDATE_BSL=1 pwsh tests/ILVerify/ilverify.ps1
```

## Baselines Location
`tests/ILVerify/*.bsl`

## Verify
Re-run without `TEST_UPDATE_BSL=1`, should pass.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/dotnet) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
