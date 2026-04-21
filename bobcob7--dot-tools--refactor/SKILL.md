---
name: refactor
description: Refactor code to improve quality without changing behavior Use when this capability is needed.
metadata:
  author: bobcob7
---

Refactor the code at `$ARGUMENTS` to improve its quality.

## Principles

1. **Preserve behavior** - Tests should pass before and after
2. **Small steps** - Make incremental changes
3. **One thing at a time** - Don't mix refactoring with new features

## Common Refactorings

### Extract
- **Extract function**: Pull out a block into a named function
- **Extract variable**: Name a complex expression
- **Extract constant**: Replace magic numbers/strings

### Simplify
- **Remove dead code**: Delete unused functions/variables
- **Simplify conditionals**: Use early returns, guard clauses
- **Flatten nesting**: Reduce indentation levels

### Rename
- **Rename for clarity**: Make names describe purpose
- **Consistent naming**: Match project conventions

### Restructure
- **Split large functions**: Single responsibility
- **Group related code**: Improve cohesion
- **Reduce coupling**: Minimize dependencies

## Process

1. **Understand** - Read the code, understand its purpose
2. **Test** - Ensure tests exist (write them if not)
3. **Plan** - Identify specific improvements
4. **Execute** - Make changes incrementally
5. **Verify** - Run tests after each change

## Output

After refactoring, summarize:
- What changed
- Why it's better
- Any follow-up improvements to consider

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/bobcob7) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
