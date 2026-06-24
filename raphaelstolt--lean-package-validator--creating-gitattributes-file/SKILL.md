---
name: creating-gitattributes-file
description: Create a lean-package-validator compatible .gitattributes file in Laravel Boost projects; use when generating missing export-ignore entries or scaffolding a new .gitattributes file. Use when this capability is needed.
metadata:
  author: raphaelstolt
---

# Create .gitattributes with lean-package-validator

Follow these steps from the repository root:

1. Run `./vendor/bin/lean-package-validator validate --create` to generate a .gitattributes file with all expected export-ignore entries.
2. If you want aligned export-ignore columns, add `--align-export-ignores`.
3. If you do not want the generated header comment, add `--omit-header`.
4. If a `.lpv` file exists, it will be used automatically; otherwise the default PHP preset is used.

If the command reports missing patterns, re-run with `--create` after verifying the repository root is correct.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/raphaelstolt) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
