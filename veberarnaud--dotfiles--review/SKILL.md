---
name: review
description: Review code changes for quality and security Use when this capability is needed.
metadata:
  author: veberarnaud
---

# Code Review

Perform a thorough code review of staged or unstaged changes.

## What to review

1. **Security**
   - No hardcoded secrets, tokens, API keys
   - No SQL injection, XSS, command injection vulnerabilities
   - Proper input validation
   - Secure authentication/authorization patterns

2. **Code quality**
   - Clear and readable code
   - Appropriate error handling
   - No unnecessary complexity
   - Consistent naming conventions

3. **Performance**
   - No obvious performance issues
   - Efficient algorithms and data structures
   - No N+1 queries or unnecessary loops

4. **Best practices**
   - Follows project conventions
   - Proper use of types (if applicable)
   - No code duplication
   - Appropriate test coverage considerations

## Process

1. Run `git diff` or `git diff --staged` to get changes
2. Analyze each changed file
3. Check for issues in each category
4. Provide actionable feedback

## Output format

For each issue found:

- **File**: path/to/file.ext:line
- **Severity**: Critical / Warning / Suggestion
- **Category**: Security / Quality / Performance / Convention
- **Issue**: Description of the problem
- **Fix**: How to address it

End with a summary: total issues by severity, overall assessment.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/veberarnaud) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
