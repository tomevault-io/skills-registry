---
name: lint
description: Check code quality and formatting before committing changes. Use this to verify your changes meet our coding standards. Use when this capability is needed.
metadata:
  author: redisearch
---

# Lint Skill

Check code quality and formatting before committing changes.

## Usage
Run this skill to check for lint errors and formatting issues.

## Instructions

1. Run the lint check:
   ```bash
   make lint
   ```

2. If clippy reports warnings or errors, fix them before proceeding

3. Check formatting:
   ```bash
   make fmt CHECK=1
   ```

4. If formatting check fails, apply formatting:
   ```bash
   make fmt
   ```

5. If license headers are missing, add them:
   ```bash
   cd src/redisearch_rs && cargo license-fix
   ```

## Common Clippy Fixes

- **Document unsafe blocks**: Add `// SAFETY:` comment explaining why the unsafe code is sound
- **Use `#[expect(...)]`**: Prefer over `#[allow(...)]` for lint suppressions

## Rust-Specific Checks

For Rust-only linting:
```bash
cd src/redisearch_rs && cargo clippy --all-targets --all-features
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/redisearch) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
