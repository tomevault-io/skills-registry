---
name: code-quality
description: Review code for simplification opportunities, identify over-engineering, and apply SOLID, YAGNI, DRY, KISS principles. Use when refactoring, reviewing PRs, or when code feels too complex. Use when this capability is needed.
metadata:
  author: koldook
---

# Code Quality Review

Challenge complexity and advocate for simple, readable, maintainable code.

## Core Principles

| Principle | Meaning                                                                                               |
|-----------|-------------------------------------------------------------------------------------------------------|
| **KISS**  | Prefer straightforward over clever. If it needs extensive comments, it's too complex.                 |
| **YAGNI** | Don't build for hypothetical futures. Remove unused code. Wait for 3 cases before abstracting.        |
| **DRY**   | But don't over-apply: duplication beats wrong abstraction. Extract only stable patterns.              |
| **SOLID** | Single responsibility, open/closed, Liskov substitution, interface segregation, dependency inversion. |

## Over-Engineering Signs

- Abstractions with only one implementation
- Factory/Strategy patterns for code that never varies
- Wrapper classes that just delegate
- Configuration for things that never change
- "Future-proofing" for requirements that don't exist

## Complexity Smells

- Methods > 20-30 lines
- Classes with > 5-7 dependencies
- Deep nesting > 3 levels
- Boolean parameters that change behavior
- Comments explaining "what" instead of "why"

## Simplification Opportunities

- Inline single-use variables
- Use early returns to reduce nesting
- Extract method for repeated logic
- Remove dead code paths
- Simplify boolean expressions

## Review Checklist

1. **Question the abstraction** - What problem does it solve? If "future flexibility" → challenge with YAGNI
2. **Count the layers** - Can we remove a layer? Is the abstraction earning its complexity?
3. **Check for speculative generality** - Parameters always the same? Methods with one caller? Classes with one
   subclass?
4. **Verify single responsibility** - Can you describe it in one sentence without "and"?

## Red Lines (Always Flag)

- Dead code that should be deleted
- Commented-out code (use version control)
- TODO comments older than current sprint
- Overly defensive programming
- Copy-pasted code with minor variations

## Green Flags (Praise)

- Simple, readable code
- Well-named variables and functions
- Appropriate use of standard library
- Tests that document behavior
- Code that's easy to delete

## Context-Specific

### Java/Spring Boot (transit-api/)

- Question excessive DTO layers
- Challenge services that just delegate to repositories
- Look for Spring annotations that could be simplified

### Angular/TypeScript (transit-ui/)

- Question unnecessary custom hooks
- Challenge prop drilling solutions (context might be simpler)
- Look for components that could be split or merged

## Response Format

```
### Assessment
Brief summary of complexity level and main concerns.

### Simplification Opportunities
1. **High impact**: Significant simplification
2. **Medium impact**: Worth doing, moderate effort
3. **Low impact**: Minor improvements

### Code Examples
Before/after for key suggestions.

### Trade-offs
Acknowledge when complexity is justified.
```

## Mantras

> "Perfection is achieved not when there is nothing more to add, but when there is nothing left to take away."

> "The cheapest, fastest, and most reliable components are those that aren't there."

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/koldook) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
