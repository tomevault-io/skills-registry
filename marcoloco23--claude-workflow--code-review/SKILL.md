---
name: code-review
description: Review code for correctness, edge cases, and adherence to project patterns. Use when this capability is needed.
metadata:
  author: marcoloco23
---

# Code Review Skill

Review checklist for code in this project.

## Review Checklist

### Correctness (CRITICAL)
- [ ] Logic is correct for all inputs
- [ ] Return values are correct types
- [ ] Algorithms are implemented correctly
- [ ] No off-by-one errors

### Edge Cases
- [ ] Empty inputs handled
- [ ] Null/None/undefined handled
- [ ] Zero values handled (division by zero)
- [ ] Boundary values handled
- [ ] Very large/small values handled
- [ ] inf/NaN values handled (if applicable)

### Error Handling
- [ ] Appropriate exceptions/errors raised
- [ ] Error messages are helpful (describe what went wrong)
- [ ] Errors don't leak sensitive information
- [ ] Errors are recoverable where possible

### Code Patterns
- [ ] Follows existing patterns in the codebase
- [ ] Consistent naming conventions
- [ ] Appropriate use of abstractions
- [ ] No unnecessary complexity

### Code Quality
- [ ] Type hints on public functions (if typed language)
- [ ] Docstrings/comments on public functions
- [ ] No code duplication
- [ ] Reasonable function/method length

### Testing
- [ ] Happy path tests exist
- [ ] Edge case tests exist
- [ ] Error case tests exist
- [ ] Tests are meaningful (not just coverage)

## Review Output Format

```markdown
### File: `path/to/file.py`
**Reviewed by**: agent
**Status**: APPROVED / ISSUES FOUND / NEEDS CHANGES

**Issues Found**:
1. [CRITICAL] Description...
2. [IMPORTANT] Description...
3. [MINOR] Description...

**Recommendations**:
- Suggestion 1
- Suggestion 2
```

## After Review

Update CONTINUITY.md:
- Add review findings to CODE REVIEW section (if exists)
- Mark review task as DONE in TASK QUEUE
- Add session log entry

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/marcoloco23) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
