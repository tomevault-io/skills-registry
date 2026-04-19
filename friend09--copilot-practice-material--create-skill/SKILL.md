---
name: python-code-quality
description: Checks Python code for quality issues including type hints, docstrings, naming conventions, and common anti-patterns. Use this skill when reviewing Python files or when asked to improve code quality. Use when this capability is needed.
metadata:
  author: friend09
---

# Python Code Quality Skill

When reviewing or improving Python code, follow these guidelines:

## Quality Checks

1. **Docstrings**: Every public function, class, and module must have docstrings (Google style)
2. **Type Hints**: All function parameters and return values must have type annotations
3. **Naming**: snake_case for functions/variables, PascalCase for classes, SCREAMING_SNAKE for constants
4. **Imports**: Group as stdlib, third-party, local. Use absolute imports.
5. **Complexity**: Functions should be < 20 lines. Cyclomatic complexity < 10.

## Anti-Patterns to Flag

- Mutable default arguments
- Bare except clauses
- Global variable mutations
- String concatenation in loops (use join())
- Nested functions deeper than 2 levels

## Quick Fix Script

For automated checks, run the quality check script:
[check_quality.py](scripts/check_quality.py)

## Style Reference

See the full style guide for detailed examples:
[style_guide.md](resources/style_guide.md)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/friend09) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
