---
name: python-code-review
description: Review Python code for quality, best practices, and common issues. Use when reviewing Python files, pull requests with Python code, or when the user asks for code review of .py files. Use when this capability is needed.
metadata:
  author: luminous-banking
---

# Python Code Review

Conduct thorough code reviews for Python projects.

## Quick Start

When reviewing Python code:

1. Read the code to understand its purpose
2. Check against the review checklist
3. Provide categorized feedback
4. Suggest specific improvements

## Review Checklist

### Code Quality
- [ ] Functions are focused and appropriately sized (< 50 lines ideal)
- [ ] Variable names are descriptive and follow snake_case
- [ ] No magic numbers (use named constants)
- [ ] DRY principle followed (no code duplication)
- [ ] Appropriate use of list comprehensions vs loops

### Type Safety
- [ ] Type hints on function signatures
- [ ] Complex types use `typing` module appropriately
- [ ] Optional values handled with `Optional[T]` or `T | None`

### Error Handling
- [ ] Exceptions are specific (not bare `except:`)
- [ ] Errors are logged appropriately
- [ ] Resources cleaned up (use context managers)
- [ ] Graceful degradation where appropriate

### Security
- [ ] No hardcoded secrets or credentials
- [ ] Input validation present
- [ ] SQL queries use parameterized statements
- [ ] File paths are sanitized

### Performance
- [ ] No N+1 query patterns
- [ ] Large data sets use generators
- [ ] Caching used where beneficial
- [ ] No unnecessary database calls in loops

### Testing
- [ ] Unit tests exist for new functions
- [ ] Edge cases are tested
- [ ] Mocking used appropriately for external dependencies

## Feedback Format

Categorize feedback by priority:

- 🔴 **Critical**: Must fix before merge (bugs, security issues)
- 🟡 **Suggestion**: Recommended improvements
- 🟢 **Nit**: Minor style or preference items

## Example Feedback

```markdown
🔴 **Critical**: Line 45 - SQL injection vulnerability
The query uses string formatting. Use parameterized queries instead:
cursor.execute("SELECT * FROM users WHERE id = ?", (user_id,))

🟡 **Suggestion**: Line 23 - Consider using a context manager
Replace explicit file.close() with a `with` statement for automatic cleanup.

🟢 **Nit**: Line 12 - Variable naming
Consider renaming `x` to `user_count` for clarity.
```

## Common Python Anti-Patterns

Watch for these issues:

1. **Mutable default arguments**: `def foo(items=[])`
2. **Bare except clauses**: `except:` catches everything
3. **Using `is` for value comparison**: Use `==` for values
4. **Not using f-strings**: Prefer f-strings over `.format()` or `%`
5. **Importing `*`**: Pollutes namespace, use explicit imports

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/luminous-banking) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
