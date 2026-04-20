---
name: code-review
description: Professional code review for quality, security, and best practices. Use when reviewing pull requests, code changes, or when asked to review code quality. Use when this capability is needed.
metadata:
  author: miicolas
---

# Professional Code Review Standards

Comprehensive guidelines for conducting thorough, constructive code reviews that improve code quality and team collaboration.

## Review Philosophy

1. **Be Constructive**: Focus on improving the code, not criticizing the author
2. **Be Specific**: Point to exact lines and explain why
3. **Prioritize**: Distinguish between blocking issues and suggestions
4. **Educate**: Explain the reasoning behind feedback
5. **Appreciate**: Acknowledge good patterns and improvements

## Review Checklist

### 🔴 Critical (Must Fix)

- [ ] **Security vulnerabilities** (SQL injection, XSS, auth bypass)
- [ ] **Data loss risks** (missing transactions, race conditions)
- [ ] **Breaking changes** to public APIs
- [ ] **Production blockers** (crashes, infinite loops)
- [ ] **Authentication/authorization** bypasses

### 🟡 Important (Should Fix)

- [ ] **Poor error handling** (uncaught errors, silent failures)
- [ ] **Performance issues** (N+1 queries, memory leaks)
- [ ] **Code duplication** that should be abstracted
- [ ] **Missing validation** on user input
- [ ] **Inconsistent patterns** with existing codebase
- [ ] **Missing tests** for critical paths
- [ ] **Incomplete error messages** for debugging

### 🟢 Suggestions (Nice to Have)

- [ ] Code style improvements
- [ ] More descriptive variable names
- [ ] Additional comments for complex logic
- [ ] Refactoring opportunities
- [ ] Performance micro-optimizations

## Review Process

### 1. Understand Context

Before reviewing code:
- Read the PR description and linked issues
- Understand the problem being solved
- Check related files for context

### 2. High-Level Review

```markdown
## Architecture & Design

- Does this change fit the existing architecture?
- Are there simpler approaches?
- Is the abstraction level appropriate?
- Does it introduce unnecessary complexity?
```

### 3. Code-Level Review

```markdown
## Code Quality

### Security
- [x] Input validation present
- [x] Authentication checked
- [ ] ⚠️ User input not sanitized in query (line 45)

### Error Handling
- [x] Errors properly caught
- [ ] ⚠️ Missing user-friendly error message (line 78)

### Testing
- [x] Unit tests added
- [ ] ⚠️ Missing edge case: empty array (test file line 23)

### Performance
- [x] No N+1 queries
- [x] Proper indexing
- [ ] 💡 Consider caching this result (line 156)

### Code Style
- [x] Follows project conventions
- [x] Meaningful variable names
- [ ] 💡 Could simplify with early return (line 92)
```

## Review Comment Templates

### Critical Issues

```markdown
🔴 **Security: SQL Injection Risk**

**Location**: `api/users.ts:45`

The user input is directly interpolated into the SQL query:
\`\`\`typescript
const query = `SELECT * FROM users WHERE name = '${userName}'`
\`\`\`

**Issue**: An attacker could inject SQL by providing: `'; DROP TABLE users; --`

**Fix**: Use parameterized queries:
\`\`\`typescript
const query = db.prepare('SELECT * FROM users WHERE name = ?').bind(userName)
\`\`\`

**Why**: Parameterized queries separate data from code, preventing injection attacks.
```

### Important Issues

```markdown
🟡 **Error Handling: Silent Failure**

**Location**: `services/payment.ts:78`

\`\`\`typescript
try {
  await processPayment(amount)
} catch (error) {
  console.log(error)
}
\`\`\`

**Issue**: The error is logged but not handled. User won't know payment failed.

**Suggestion**:
\`\`\`typescript
try {
  await processPayment(amount)
} catch (error) {
  console.error('Payment failed:', error)
  throw new PaymentError('Payment processing failed. Please try again.')
}
\`\`\`

**Why**: Users need feedback when operations fail, and errors should propagate to be handled at the appropriate level.
```

### Suggestions

```markdown
💡 **Suggestion: Simplify with Early Return**

**Location**: `utils/validator.ts:92`

Current:
\`\`\`typescript
function validate(data) {
  let isValid = false
  if (data) {
    if (data.email) {
      if (data.email.includes('@')) {
        isValid = true
      }
    }
  }
  return isValid
}
\`\`\`

Consider early returns for readability:
\`\`\`typescript
function validate(data) {
  if (!data?.email) return false
  return data.email.includes('@')
}
\`\`\`

**Benefits**: Reduces nesting and makes the logic easier to follow.
```

### Positive Feedback

```markdown
✅ **Great!** Excellent error handling with specific error messages and proper HTTP codes.

✅ **Nice refactor!** Extracting this logic into a reusable service makes testing much easier.
```

## Language-Specific Patterns

### TypeScript / JavaScript

```markdown
## Common Issues

### Missing Type Safety
\`\`\`typescript
// ❌ Avoid 'any'
function process(data: any) { }

// ✅ Use specific types
function process(data: UserData) { }
\`\`\`

### Poor Error Handling
\`\`\`typescript
// ❌ Swallowing errors
.catch(err => console.log(err))

// ✅ Proper error handling
.catch(err => {
  logger.error('Failed to fetch user', err)
  throw new APIError('Unable to load user data')
})
\`\`\`

### Missing Input Validation
\`\`\`typescript
// ❌ No validation
app.post('/users', (req, res) => {
  const user = req.body
})

// ✅ Validate with schema
app.post('/users', zValidator('json', userSchema), (req, res) => {
  const user = req.valid('json')
})
\`\`\`
```

### Swift / iOS

```markdown
## Common Issues

### Force Unwrapping
\`\`\`swift
// ❌ Force unwrap can crash
let name = user.name!

// ✅ Safe unwrapping
guard let name = user.name else { return }
// or
let name = user.name ?? "Unknown"
\`\`\`

### Missing @MainActor
\`\`\`swift
// ❌ UI update off main thread
Task {
  self.isLoading = false  // Could crash
}

// ✅ Ensure main actor
@MainActor
func updateUI() {
  self.isLoading = false
}
\`\`\`

### Memory Leaks
\`\`\`swift
// ❌ Strong reference cycle
service.onComplete = {
  self.handleResult()  // Retains self
}

// ✅ Weak self
service.onComplete = { [weak self] in
  self?.handleResult()
}
\`\`\`
```

## Security Review Checklist

### Authentication & Authorization

```markdown
- [ ] All protected endpoints check authentication
- [ ] User can only access their own resources
- [ ] Admin actions require admin role
- [ ] Tokens are validated and not expired
- [ ] Session management is secure
```

### Input Validation

```markdown
- [ ] All user input is validated
- [ ] File uploads have size/type restrictions
- [ ] SQL injection prevention (parameterized queries)
- [ ] XSS prevention (output encoding)
- [ ] CSRF protection for state-changing operations
```

### Data Protection

```markdown
- [ ] Passwords are hashed (not encrypted or plain text)
- [ ] Sensitive data is not logged
- [ ] API keys/secrets are in environment variables
- [ ] Personal data follows privacy regulations
- [ ] Database queries use prepared statements
```

### Network Security

```markdown
- [ ] HTTPS enforced in production
- [ ] CORS configured (not allowing all origins)
- [ ] Rate limiting on sensitive endpoints
- [ ] API keys transmitted securely
- [ ] No sensitive data in URLs
```

## Performance Review

### Database Queries

```markdown
🔴 **N+1 Query Problem**

\`\`\`typescript
// ❌ One query per user
const users = await db.select().from(users)
for (const user of users) {
  user.posts = await db.select().from(posts).where(eq(posts.userId, user.id))
}

// ✅ Single query with join
const users = await db
  .select()
  .from(users)
  .leftJoin(posts, eq(users.id, posts.userId))
\`\`\`
```

### Caching Opportunities

```markdown
💡 **Consider Caching**

This data is fetched on every request but rarely changes:
\`\`\`typescript
app.get('/config', async (c) => {
  const config = await db.select().from(appConfig)  // Cache this!
  return c.json(config)
})
\`\`\`
```

## Testing Review

### Test Coverage

```markdown
## Testing Feedback

✅ Good coverage of happy path
🟡 Missing edge cases:
  - [ ] Empty array handling
  - [ ] Null/undefined inputs
  - [ ] Network failure scenarios
  - [ ] Concurrent requests
```

### Test Quality

```markdown
### Test Improvements

\`\`\`typescript
// ❌ Unclear test
it('works', () => {
  expect(result).toBe(true)
})

// ✅ Descriptive test
it('should return true when user is authenticated', () => {
  const result = checkAuth({ token: 'valid-token' })
  expect(result).toBe(true)
})
\`\`\`
```

## Documentation Review

```markdown
## Documentation Feedback

🟡 Consider adding:
- Function documentation for complex public APIs
- README update for new feature
- Migration guide for breaking changes
- Example usage for new components

✅ Good inline comments explaining the 'why' not the 'what'
```

## Giving Effective Feedback

### ❌ Poor Feedback

```markdown
"This code is bad"
"Why did you do it this way?"
"This won't work"
```

### ✅ Good Feedback

```markdown
"This approach could lead to race conditions when multiple users update simultaneously. Consider using database transactions or optimistic locking."

"I see you're duplicating this logic in 3 places. Could we extract it into a shared function? This would make future changes easier and reduce the chance of bugs."

"Great use of TypeScript discriminated unions here! This makes the error handling much more type-safe."
```

## Review Summary Template

```markdown
## Review Summary

### ✅ Strengths
- Excellent test coverage
- Clear, well-structured code
- Good error handling

### 🔴 Blocking Issues (Must Fix)
1. Security: User input not validated (line 45)
2. Bug: Race condition in concurrent updates (line 78)

### 🟡 Important (Should Fix)
1. Performance: N+1 query issue (line 156)
2. Error handling: Silent failure on payment (line 203)

### 💡 Suggestions (Optional)
1. Consider extracting common logic into utility (line 92)
2. Could simplify with early returns (line 145)

### 📝 Questions
1. Have you tested this with large datasets (>10k items)?
2. Does this handle the edge case when the array is empty?

### Overall Assessment
Solid implementation with good structure. Please address the security issue and race condition before merging. The performance optimization would be great to include but not blocking.
```

## Approval Criteria

### ✅ Approve

- No critical or important issues
- Tests pass
- Code follows project standards
- Changes match PR description

### 🔄 Request Changes

- Critical security issues
- Breaking bugs
- Missing essential tests
- Doesn't solve the stated problem

### 💬 Comment

- Minor suggestions
- Questions for clarification
- Nice-to-have improvements
- Learning opportunities

## Quick Reference

### Emoji Legend

- 🔴 Critical (blocking)
- 🟡 Important (should fix)
- 💡 Suggestion
- ✅ Positive feedback
- ❓ Question
- 📝 Documentation

### Review Time Guidelines

- Small PR (<100 lines): 10-15 minutes
- Medium PR (100-500 lines): 30-45 minutes
- Large PR (>500 lines): Consider splitting

### Red Flags

- PRs mixing multiple unrelated changes
- Missing tests for new features
- Commented-out code
- Debug console.log statements
- Hardcoded credentials or secrets
- Massive files (>500 lines)

## Additional Resources

- [Google's Code Review Guidelines](https://google.github.io/eng-practices/review/)
- [OWASP Top 10](https://owasp.org/www-project-top-ten/)
- [Code Review Best Practices](https://blog.codinghorror.com/code-reviews-just-do-it/)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/miicolas) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
