---
name: hsm-module-helper
description: Work on any HSM module - explore code, implement features, run tests, benchmarks Use when this capability is needed.
metadata:
  author: stxkxs
---

# HSM Module Helper

Quick helper for working on HSM modules.

## Usage

```
/hsm-module <module-number>
```

## What You Do

When user invokes this skill with a module number (1-9):

1. **Navigate to module:**
   ```bash
   cd crates/<module-name>
   ```

2. **Show module status:**
   - Show recent changes (`git log -5 --oneline -- .`)
   - Check compilation (`cargo check`)
   - List available plans in `docs/phases/`

3. **Offer actions:**
   - "Explore code" - Read and explain module
   - "Implement feature" - User describes what to add
   - "Run tests" - `cargo test`
   - "Run benchmarks" - `cargo bench`
   - "Security audit" - `cargo audit && cargo clippy`
   - "View docs" - Show README and plans

Let user choose what they want to do with the module.

## Module Mapping

| Number | Crate           | Purpose                        |
|--------|-----------------|--------------------------------|
| 1      | `crypto-engine` | Core cryptographic primitives  |
| 2      | `key-manager`   | Key lifecycle management       |
| 3      | `auth`          | Authentication & authorization |
| 4      | `grpc-api`      | gRPC API server                |
| 5      | `audit`         | Audit logging                  |
| 6      | `metrics`       | Metrics & monitoring           |
| 7      | `storage`       | Persistent storage             |
| 8      | `backup`        | Backup & recovery              |
| 9      | `config`        | Configuration management       |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/stxkxs) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
