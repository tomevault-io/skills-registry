---
name: code-quality-hint
description: Notices code quality issues when viewing code with long functions, deep nesting, duplicated code, unclear naming, or obvious anti-patterns. Use when user shows code that might benefit from refactoring or has maintainability concerns. Use when this capability is needed.
metadata:
  author: alexeytriplea
---

# Code Quality Hint

Lightweight code quality awareness for conversations. Provides quick hints about maintainability issues without full review.

## When to activate

Activate when you notice in code being discussed:
- Functions longer than 50-60 lines
- Nesting deeper than 3 levels
- Duplicated code blocks
- Unclear variable names (x, data, temp, result)
- Missing error handling
- TODO/FIXME comments
- Commented-out code
- Magic numbers without explanation
- Callback hell
- Large switch statements

## What to do

**Briefly mention** the concern in 1-2 sentences, then suggest running a command:

**Example responses:**

```
"This function is quite long (80+ lines). Consider splitting it for better maintainability.
Run `/review src/service.js` for refactoring suggestions."
```

```
"I see duplicated logic on lines 23-35 and 78-90.
Run `/quick-check` to identify code quality improvements."
```

```
"Variable names like 'x', 'temp', and 'data' make this code hard to follow.
Run `/deep-review` for comprehensive code quality analysis and best practices."
```

## Important rules

- ❌ Do NOT perform full code review
- ❌ Do NOT refactor code automatically
- ✅ Only mention clear issues in current conversation
- ✅ Keep hints brief (1-2 sentences)
- ✅ Always suggest a command for detailed review
- ✅ Be constructive, not critical

## Common patterns to flag

**Function size:**
- Functions > 50 lines (suggest splitting)
- Functions doing multiple things

**Nesting:**
- More than 3 levels deep
- Early returns could flatten structure

**Naming:**
- Single letter variables (except loop counters)
- Generic names (data, result, temp, x)
- Inconsistent naming conventions

**Code smells:**
- Duplicated blocks
- God functions/classes
- Long parameter lists
- Commented-out code
- console.log in production code

**Error handling:**
- Missing try-catch
- Swallowed errors
- No input validation

## What NOT to flag

- Style preferences (tabs vs spaces)
- Formatting (handled by formatters)
- Minor optimizations
- Subjective choices

## Commands to suggest

- `/quick-check` - for fast quality check
- `/review [file]` - for specific file analysis
- `/deep-review` - for comprehensive quality review with Context7 (checks against current best practices)

This is a hint tool to improve awareness during normal coding conversations.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/alexeytriplea) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
