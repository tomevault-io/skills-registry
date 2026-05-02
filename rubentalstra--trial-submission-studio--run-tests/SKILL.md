---
name: run-tests
description: Run tests for a specific crate or domain Use when this capability is needed.
metadata:
  author: rubentalstra
---
# Run Domain Tests

Execute targeted test suites for specific crates or functionality.

## Usage
- `/run-tests` - Run all tests
- `/run-tests <crate>` - Run tests for specific crate (e.g., `/run-tests tss-submit`)
- `/run-tests <crate> <filter>` - Run filtered tests (e.g., `/run-tests tss-submit validate`)

## Commands
```bash
# All tests
cargo test

# Specific crate
cargo test --package tss-submit
cargo test --package tss-standards
cargo test --package tss-ingest
cargo test --package tss-gui

# Filtered tests
cargo test --package tss-submit validate
cargo test --package tss-submit export
cargo test --package tss-submit normalize
```

## Test Categories
- **Unit tests**: Pure function tests (inline with `#[cfg(test)]`)
- **Integration tests**: Full pipeline with mock CSV data
- **Validation tests**: CDISC compliance checking

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rubentalstra) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
