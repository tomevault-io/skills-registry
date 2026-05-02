---
name: clean-code
description: description: Guide for writing clean, maintainable, professional code. Covers meaningful naming, function design, formatting, error handling, comments, and code smells. Use when this capability is needed.
metadata:
  author: artefaritakuniklo
---
---
name: clean-code
description: Guide for writing clean, maintainable, professional code. Covers meaningful naming, function design, formatting, error handling, comments, and code smells.
---

# Clean Code

Professional code craftsmanship based on Robert C. Martin's principles.

## When to Use
- Writing/refactoring code
- Code review and quality assessment
- Establishing coding standards
- Detecting and fixing code smells

## Quick References
- **Quick start:** `references/quick-start.md`
- **Theory:** `CORE_PRACTICES.md`
- **Language-specific:** `references/naming-conventions.md`
- **Code smells:** `references/code-smells.md`

## Core Principles

### 1. Meaningful Names
Names reveal intent and enable understanding without reading implementation.

**Rules:**
- Pronounceable, searchable, revealing intent
- Avoid `l` (lowercase L) or `O` (uppercase o)
- Classes: nouns (`Customer`, `Account`)
- Methods: verbs (`postPayment()`, `save()`)
- One word per concept
- Professional terminology

**Bad:** `def d(): return (currentTime() - lastModifiedTime()) / 86400`
**Good:** `def get_days_since_last_modification(): ...`

### 2. Functions
Functions should be small, focused, and single-purpose.

**Rules:**
- Small (ideally <20 lines)
- Do one thing well (SRP)
- Minimal parameters (0-2 ideal, max 3)
- No side effects beyond stated purpose
- Use exceptions, not error codes
- Extract duplication (DRY)
- No flag parameters

**Bad:** `process_user_data(data, flag1, flag2, flag3)` - 100 lines, multiple purposes
**Good:** Decompose into focused functions with clear names

### 3. Comments
Self-documenting code is best. Comments explain WHY, not WHAT.

**Write comments for:** Business logic, non-obvious decisions, warnings, legal info
**Don't write for:** Obvious code, redundant text, commented-out code

### 4. Formatting
Code formatting improves readability. Choose conventions and follow consistently.

**Key rules:**
- Consistent indentation (2-4 spaces)
- Lines under 80-120 characters
- Blank lines separate logical groups
- Consistent bracket placement
- Group related code together

### 5. Error Handling
Handle errors explicitly and clearly. Use exceptions over error codes.

**Key rules:**
- Use exceptions, not error codes
- Provide context with exceptions
- Custom exception types for different scenarios
- Use try-finally or context managers for cleanup
- Don't silently catch and ignore errors
- Avoid returning null (use exceptions or special objects)

### 6. Code Smells Detection
Recognize patterns that indicate deeper problems requiring refactoring.

See `references/code-smells.md` for comprehensive list of code smells and refactoring techniques.

**Common Smells:**
- **Duplicate Code** - Extract to shared function
- **Long Methods** - Break into smaller focused functions
- **Long Parameter Lists** - Use object parameters or refactor
- **Divergent Change** - One class changed for multiple reasons (violates single responsibility)
- **Shotgun Surgery** - One change requires edits across many classes
- **Feature Envy** - Method uses more of another class than its own
- **Data Clumps** - Groups of variables passed together (extract to objects)
- **Magic Numbers** - Use named constants

## Workflow: Code Review & Refactoring

**Review:** Check names, functions, comments, formatting, error handling, code smells
**Refactor:** Identify issues → write tests → make changes → run tests → document

## Key Takeaways

1. Code is read far more than written - optimize for readability
2. Names reveal intent - spend time on meaningful names
3. Functions stay small and focused - one responsibility each
4. Comments explain WHY, not WHAT
5. Exceptions over error codes or null returns
6. Refactoring is ongoing - clean code requires continuous improvement

---

**Source:** Clean Code: A Handbook of Agile Software Craftsmanship by Robert C. Martin

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/artefaritakuniklo) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
