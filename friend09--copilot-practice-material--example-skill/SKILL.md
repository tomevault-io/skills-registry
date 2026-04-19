---
name: python-lint-style
description: Checks Python code for linting issues, style violations, and common anti-patterns. Use this skill when reviewing Python files, fixing lint errors, improving code style, checking for missing docstrings, verifying naming conventions, or organizing imports. Use when this capability is needed.
metadata:
  author: friend09
---

# Python Linting and Style Checker

## When to Use

Use this skill when you need to:

- Review Python code for style compliance
- Find missing or incomplete docstrings
- Check naming conventions (PEP 8 and team standards)
- Verify import ordering and grouping
- Identify common anti-patterns and code smells
- Enforce type hint requirements

## Instructions

Follow these steps when checking Python code:

### 1. Check Docstrings

- Every module, class, and public function must have a docstring
- Use Google-style docstring format (see `resources/style_guide.md`)
- Docstrings must include: summary, Args, Returns, and Raises sections where applicable
- Flag any public function missing a docstring as a **high-priority** issue

### 2. Check Type Hints

- All function parameters must have type annotations
- All functions must have return type annotations
- Use `Optional[T]` for parameters that can be `None`
- Use modern syntax (`list[str]` instead of `List[str]`) for Python 3.9+

### 3. Check Naming Conventions

- Variables and functions: `snake_case`
- Classes: `PascalCase`
- Constants: `UPPER_SNAKE_CASE`
- Private members: prefix with single underscore `_`
- Avoid single-letter variable names except in comprehensions and lambdas

### 4. Check Import Order

Imports must be grouped in this order with a blank line between each group:

1. Standard library imports
2. Third-party library imports
3. Local application imports

Within each group, imports should be sorted alphabetically.

### 5. Check Complexity

- Functions should not exceed 20 lines of logic (excluding docstrings and blank lines)
- Maximum function parameter count: 5 (use a dataclass or config object for more)
- Cyclomatic complexity should stay below 10
- Nesting depth should not exceed 3 levels

## Tools

You can run the automated lint checker for a quick scan:

- **Script**: `scripts/lint_check.py` -- runs docstring, naming, and import checks
- **Usage**: `python scripts/lint_check.py <filepath>`

## Reference

For detailed team conventions, refer to:

- **Style Guide**: `resources/style_guide.md` -- team-specific Python style conventions

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/friend09) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
