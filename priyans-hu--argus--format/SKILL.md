---
name: format
description: Format code according to project standards. Use after making changes or before committing. Use when this capability is needed.
metadata:
  author: priyans-hu
---

# Format - argus

Format code according to project standards.

## Command

```bash
go fmt ./...
```

## Description

Format all Go files

## Usage

- Run before committing changes
- Use `-d` (diff only) to check without modifying files
- Format specific files by passing paths as arguments

## Notes

- This command modifies files in place
- Formatting is enforced by pre-commit hooks (if configured)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/priyans-hu) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
