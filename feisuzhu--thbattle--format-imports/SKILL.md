---
name: format-imports
description: Rewrite Python script's top level import statements to comply coding standard Use when this capability is needed.
metadata:
  author: feisuzhu
---

# Format Imports

Rewrite Python script's top level import statements to comply coding standard

## Usage

Run the deployment script: `scripts/format-imports.py --files <target>`. The script have shebang and executable bit set, run it directly.
The script will rewrite the target inplace.
The script will only modify top level import statements, other code will be left untouched.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/feisuzhu) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
