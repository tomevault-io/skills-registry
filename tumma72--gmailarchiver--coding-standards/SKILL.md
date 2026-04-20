---
name: coding-standards
description: >- Use when this capability is needed.
metadata:
  author: tumma72
---

# Coding Standards for GMailArchiver

This skill provides guidance on coding standards and style conventions.

## Source Documentation

**Always read the authoritative source:**

**`docs/CODING.md`** - The definitive coding standards document containing:
- Code style and formatting rules
- Naming conventions
- Import organization
- Type hint requirements
- Docstring standards
- Error handling patterns
- Logging conventions

## Quick Reference

Key tools used:
- **ruff** - Linting and formatting (line length: 100)
- **mypy** - Type checking (strict mode)
- **Python 3.14+** - Target version

## Quality Commands

```bash
# Check linting
uv run ruff check .

# Auto-fix linting issues
uv run ruff check . --fix

# Check formatting
uv run ruff format --check .

# Apply formatting
uv run ruff format .

# Type checking
uv run mypy src/gmailarchiver
```

## Usage

When writing code:
1. Read `docs/CODING.md` for current coding standards
2. Run quality checks before committing
3. If standards change, update `docs/CODING.md` (not this skill)

The source documentation is the **single source of truth** - this skill just points you there.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tumma72) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
