---
name: audit-refactor-opportunities
description: Identify code smells and refactoring opportunities without fixing. Use for tech debt assessment. Use when this capability is needed.
metadata:
  author: dthompson-jti
---

# Audit Refactor Opportunities

Identify code smells and refactoring opportunities.

## When to Use
- Tech debt assessment
- Planning refactoring sprints
- Understanding codebase health

## Approach

### Step 1: Project Invariants (Required)
**Before auditing**, check `docs/knowledge-base/` for code style constraints:
- `AGENTS.md` — established patterns
- `RULES-*.md` — code organization rules
- Flag any violation of documented patterns as higher priority.

### Step 2: Focus Areas
- **Code Smells**: Duplication, long functions, deep nesting
- **Pattern Violations**: Inconsistent patterns across similar code
- **Naming**: Unclear or inconsistent naming
- **Complexity**: Overly complex logic that could be simplified
- **Dead Code**: Unused exports, commented code

### Checklist
- [ ] Duplicated logic that could be shared
- [ ] Functions >50 lines that could be split
- [ ] Deeply nested conditionals (>3 levels)
- [ ] Inconsistent patterns for similar operations
- [ ] Unclear variable/function names
- [ ] Dead or commented-out code

### Output
Prioritized list of refactoring opportunities with effort estimates (Low/Medium/High).

## Constraints
- **Identify only** — do NOT fix
- Fixes go through `/refactor` or `/quick-fix`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/dthompson-jti) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
