---
name: code-review
description: Provide thorough, constructive code reviews that improve code quality and share knowledge. Use when this capability is needed.
metadata:
  author: hffmnnj
---

# Code Review Skill

## Purpose
Provide thorough, constructive code reviews that improve code quality and share knowledge.

## Review Focus Areas

### 1. Correctness
- Does the code do what it claims?
- Are edge cases handled?
- Are there potential bugs?

### 2. Design
- Is the code well-structured?
- Are abstractions appropriate?
- Does it follow project patterns?

### 3. Performance
- Are there obvious performance issues?
- Unnecessary loops or allocations?
- Appropriate data structures?

### 4. Security
- Input validation present?
- Secrets properly handled?
- SQL injection, XSS risks?

### 5. Maintainability
- Is the code readable?
- Are names meaningful?
- Is complexity manageable?

## Review Process

1. Understand the context and goal
2. Read the code thoroughly
3. Run the code if possible
4. Leave specific, actionable comments
5. Distinguish between blocking issues and suggestions
6. Acknowledge good code, not just problems

## Comment Types

- **Blocking** - Must be fixed before merge
- **Suggestion** - Nice to have, not required
- **Question** - Need clarification
- **Nitpick** - Style preference (prefix with "nit:")

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hffmnnj) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
