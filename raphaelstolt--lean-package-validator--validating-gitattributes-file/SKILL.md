---
name: validating-gitattributes-file
description: Validate .gitattributes files in Laravel Boost projects using lean-package-validator; use when checking export-ignore completeness, ordering, or stale entries. Use when this capability is needed.
metadata:
  author: raphaelstolt
---

# Validate .gitattributes with lean-package-validator

From the repository root:

1. Run `./vendor/bin/lean-package-validator validate` to check the current .gitattributes file.
2. Add `--diff` to see the expected content when validation fails.
3. Add `--enforce-strict-order` or `--enforce-alignment` only if strict comparison is required.
4. Add `--report-stale-export-ignores` (with `--diff`) to surface export-ignores that no longer exist.

If validation fails, use the updating skill to bring .gitattributes in sync.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/raphaelstolt) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
