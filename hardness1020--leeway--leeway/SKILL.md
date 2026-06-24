---
name: coding-standards
description: Coding standards and best practices checklist for Python and TypeScript. Use when reviewing or writing code to enforce naming conventions, structure guidelines, error handling patterns, and documentation standards. Apply to all workflow nodes that involve code generation or modification. Use when this capability is needed.
metadata:
  author: hardness1020
---

# Coding Standards

Check code against these standards when reviewing or writing.

## Naming
- Use clear, descriptive names for variables and functions
- Prefix booleans with `is_`, `has_`, `can_`, `should_`
- Use UPPER_SNAKE_CASE for constants
- Use PascalCase for classes, snake_case (Python) or camelCase (JS/TS) for functions

## Structure
- Keep functions to one responsibility, under 50 lines
- Limit nesting to 3 levels of indentation
- Group related code; separate unrelated code
- Organize imports: stdlib, third-party, local

## Error Handling
- Handle errors at the appropriate level — never swallow them
- Write descriptive, actionable error messages
- Clean up resources properly (context managers, try/finally)

## Documentation
- Add docstrings/JSDoc to public APIs
- Use inline comments to explain *why*, not *what*
- Keep README up to date with setup instructions

For language-specific conventions, see [references/python.md](references/python.md) or [references/typescript.md](references/typescript.md).

---
> Source: [hardness1020/Leeway](https://github.com/hardness1020/Leeway) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-21 -->
