---
name: implementing-code
description: > Use when this capability is needed.
metadata:
  author: aiskillstore
---

# Task Input

- **Purpose**: What to achieve
- **Deliverable**: Completion criteria

# Implementation Flow

1. Investigate related code (Glob, Grep, Read)
2. Implement following existing patterns
3. Commit changes

# Commit Convention

Check CLAUDE.md for project-specific rules.

Default format (Conventional Commits):
```
<type>: <description>
```

| type | usage |
|------|-------|
| feat | New feature |
| fix | Bug fix |
| docs | Documentation |
| refactor | Refactoring |
| test | Tests |
| chore | Other |

# Commit Execution

```bash
git add <files>
git commit -m "<type>: <description>"
```

# Parallel Execution

When running in parallel with other tasks:
- Do NOT edit the same files
- Report conflicts to manager

# Completion Report

- Changed files
- Implementation summary
- Commit hash

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/aiskillstore) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
