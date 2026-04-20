---
name: cargo-shear
description: Finds and removes unused dependencies from Rust projects. Use when cleaning up a Rust repository, removing dead deps, or auditing Cargo.toml. Use when this capability is needed.
metadata:
  author: onbjerg
---

# cargo-shear

Detects and removes unused dependencies from Cargo.toml files.

## Usage

### Find unused dependencies

```bash
cargo shear
```

### Fix automatically (remove unused deps)

```bash
cargo shear --fix
```

## Key Options

- `--fix` - Automatically remove unused dependencies from Cargo.toml
- `--ignore <CRATE>` - Ignore specific crate (for false positives)

## Workflow

1. Run `cargo shear` to list unused deps
2. Review the output for false positives (proc macros, build deps, optional features)
3. Run `cargo shear --fix` to remove them
4. Run `cargo build` to verify nothing broke

## Common False Positives

Some deps may appear unused but are actually needed:
- Proc macro crates (e.g., `serde_derive`)
- Build dependencies
- Crates used only via feature flags
- Crates re-exported by other crates

Use `--ignore <CRATE>` for legitimate false positives.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/onbjerg) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-12 -->
