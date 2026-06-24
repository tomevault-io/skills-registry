---
name: release-preparation
description: Steps to verify code quality and functionality before releasing. Use when this capability is needed.
metadata:
  author: zpvip
---

# Release Preparation

## Running Tests

Run the test suite with:

```bash
bundle exec rspec
```

## Code Quality Checks

After completing development work, run these commands in order:

### 1. RuboCop (Linting)

```bash
bundle exec rubocop -f github
```

Fix any style violations before committing. The `-f github` flag outputs in a format suitable for CI/GitHub Actions.

### 2. Security Audit (Dependencies)

Check for vulnerable gem versions.

```bash
bundle exec bundle audit check --update
```

If vulnerabilities are found, update the affected gems:
```bash
bundle update [gem_name]
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/zpvip) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
