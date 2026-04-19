---
name: compare-api-versions
description: Compare OpenAPI versions for Terraform provider updates Use when this capability is needed.
metadata:
  author: polytomic
---

# Compare API Versions

Compares OpenAPI spec versions from polytomic/fern-config to identify changes that need to be reflected in the Terraform provider. Automatically detects the current polytomic-go SDK version from go.mod.

## Usage

Compare current version (from go.mod) with a new version:
```
compare-api-versions go@1.13.0
```

Compare two specific versions:
```
compare-api-versions go@1.11.0 go@1.12.0
```

## Output

The skill reports:
- New, removed, and modified API endpoints
- New, removed, and modified schemas
- Potentially breaking changes
- Summary of impact areas for the Terraform provider

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/polytomic) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
