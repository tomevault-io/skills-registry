---
name: plan
description: | Use when this capability is needed.
metadata:
  author: anilcancakir
---

# Implementation Planning Skill

Guidelines for creating effective implementation plans.

## When to Use Planning

### Plan When
- Feature spans multiple files/components
- Architecture decisions needed
- Multiple valid approaches exist
- Dependencies need coordination
- Risk of rework is high

### Skip Planning When
- Single file change
- Bug fix with clear solution
- Following existing pattern exactly
- Simple CRUD operation

## Planning Phases

### 1. Requirements Clarification
- What exactly needs to be built?
- What are the acceptance criteria?
- What constraints exist? (performance, security, compatibility)
- Is TDD expected?

### 2. Codebase Analysis
- Explore existing structure
- Find similar implementations
- Identify patterns and conventions
- Map dependencies

### 3. Solution Design
- Break into logical phases
- Order by dependencies
- Define atomic tasks
- Add verification steps

### 4. Documentation
- Save structured plan
- Include specific file paths
- Add verification commands

## Plan File Location

```
.claude/plans/{YYYYMMDD}-{HHMMSS}-{slug}.md
```

## Analysis Strategies

### Standard Analysis
1. Read CLAUDE.md for conventions
2. Use Grep/Glob to find similar code
3. Read key files to understand patterns
4. Map where new code should live

### Serena-Enhanced Analysis
1. `get_symbols_overview()` - file structure
2. `find_symbol()` - locate classes/functions
3. `find_referencing_symbols()` - dependency mapping
4. `read_memory()` - project knowledge

## Task Granularity

Good task:
```markdown
### Task 1.1: Create User Model
- **File**: app/Models/User.php
- **Action**: Create
- **Verification**: `php artisan tinker` → `new User()` works
```

Too vague:
```markdown
### Task 1.1: Set up user system
- **Action**: Create everything for users
```

## Verification Types

| Type | Example |
|------|---------|
| Command | `php artisan test --filter=UserTest` |
| File Check | File exists at expected path |
| Syntax | No errors when loading file |
| Manual | User confirms behavior |

## Reference

For detailed plan format, see [references/plan-format.md](references/plan-format.md).

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/anilcancakir) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
