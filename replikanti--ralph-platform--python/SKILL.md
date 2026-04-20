---
name: python
description: Best practices and patterns for Python development Use when this capability is needed.
metadata:
  author: replikanti
---

# Python Development Guide

## Code Style
- Follow PEP 8 (enforced by Ruff)
- Use type hints for all public functions
- Prefer f-strings over .format() or %

## Common Patterns
- Use dataclasses or Pydantic for data structures
- Use context managers for resource management
- Prefer pathlib over os.path

## Testing
- Use pytest with descriptive test function names
- Use fixtures for test setup
- Use parametrize for multiple test cases

## Error Handling
- Create custom exceptions inheriting from appropriate base
- Use `raise ... from e` to preserve stack traces
- Document exceptions in docstrings

## Type Checking
- Run `mypy --ignore-missing-imports .` before committing
- Use `Optional[T]` for nullable values
- Use `TypeVar` for generic functions

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/replikanti) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
