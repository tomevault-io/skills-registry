---
name: code-review
description: Review code for quality and best practices Use when this capability is needed.
metadata:
  author: linjh1118
---

# Code Review

Review the specified code for:

## Quality Checks

1. **Code Style**
   - Consistent naming conventions
   - Proper indentation and formatting
   - No unused imports or variables

2. **Logic**
   - Edge case handling
   - Error handling
   - Null/undefined checks

3. **Performance**
   - Unnecessary re-renders (React)
   - N+1 queries
   - Memory leaks

4. **Security**
   - Input validation
   - SQL injection prevention
   - XSS prevention

## Output Format

For each issue found:
- File and line number
- Issue description
- Suggested fix
- Severity (high/medium/low)

---
> Source: [linjh1118/claude-code-tutorial](https://github.com/linjh1118/claude-code-tutorial) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-22 -->
