---
name: code-review-checklist
description: Code review checklist for quality, security, and maintainability. Use after writing or modifying code to ensure high standards. Covers code quality, security, performance, and best practices. Use when this capability is needed.
metadata:
  author: hellanglez
---

# Code Review Checklist

Comprehensive checklist for reviewing code changes to ensure quality, security, and maintainability.

## Quick Review Checklist

Before approving code:
- [ ] Code is simple and readable
- [ ] Functions/variables well-named
- [ ] No duplicated code
- [ ] Proper error handling
- [ ] No hardcoded secrets
- [ ] Input validation implemented
- [ ] Tests included
- [ ] No console.log statements

## Security Checks (CRITICAL)

### Secrets & Credentials
- [ ] No hardcoded API keys
- [ ] No hardcoded passwords/tokens
- [ ] No credentials in comments
- [ ] .env in .gitignore
- [ ] Secrets in environment variables

### Input Validation
- [ ] All user input validated
- [ ] SQL queries parameterized
- [ ] Output escaped (XSS prevention)
- [ ] File uploads validated
- [ ] Path traversal prevented

### Authentication & Authorization
- [ ] Auth required on protected routes
- [ ] Users can only access their data
- [ ] Admin routes require admin role
- [ ] JWT properly validated
- [ ] Sessions secure

## Code Quality (HIGH)

### Function Quality
- [ ] Functions < 50 lines
- [ ] Single responsibility
- [ ] Clear, descriptive names
- [ ] Minimal parameters (< 5)
- [ ] No deeply nested logic (< 4 levels)

### File Organization
- [ ] Files < 800 lines
- [ ] Logical grouping
- [ ] Clear directory structure
- [ ] Related code together
- [ ] Separation of concerns

### Code Clarity
- [ ] Self-documenting code
- [ ] Comments explain "why", not "what"
- [ ] No magic numbers
- [ ] Consistent naming conventions
- [ ] No abbreviations (unless standard)

### Error Handling
- [ ] Try-catch blocks where needed
- [ ] Errors logged properly
- [ ] User-friendly error messages
- [ ] Error propagation correct
- [ ] No swallowed errors

### Immutability
- [ ] No object mutation
- [ ] Use spread operator for copies
- [ ] Array methods (map, filter, reduce)
- [ ] No direct array modification
- [ ] Const by default

**Example**:
```javascript
// ❌ BAD: Mutation
function updateUser(user, name) {
  user.name = name  // Mutates!
  return user
}

// ✅ GOOD: Immutability
function updateUser(user, name) {
  return {
    ...user,
    name
  }
}
```

## Testing (HIGH)

### Test Coverage
- [ ] New code has tests
- [ ] Edge cases covered
- [ ] Error paths tested
- [ ] Integration tests for APIs
- [ ] E2E tests for critical flows

### Test Quality
- [ ] Tests are independent
- [ ] No flaky tests
- [ ] Clear test names
- [ ] Proper setup/teardown
- [ ] Mocks used appropriately

## Performance (MEDIUM)

### Algorithms
- [ ] Time complexity acceptable
- [ ] No O(n²) when O(n log n) possible
- [ ] Efficient data structures used
- [ ] No unnecessary iterations
- [ ] Database queries optimized

### React Performance
- [ ] No unnecessary re-renders
- [ ] useMemo for expensive calculations
- [ ] useCallback for stable callbacks
- [ ] Keys on list items
- [ ] Code splitting where appropriate

### Database
- [ ] Indexes on queried columns
- [ ] No N+1 queries
- [ ] Pagination for large datasets
- [ ] Connection pooling used
- [ ] Query limits set

### Caching
- [ ] Cache expensive operations
- [ ] Cache invalidation strategy
- [ ] No stale data issues
- [ ] Cache keys well-designed

## Best Practices (MEDIUM)

### Dependencies
- [ ] No unnecessary dependencies
- [ ] Dependencies up to date
- [ ] `npm audit` clean
- [ ] License compatibility checked
- [ ] Bundle size considered

### Accessibility
- [ ] ARIA labels where needed
- [ ] Keyboard navigation works
- [ ] Color contrast sufficient
- [ ] Screen reader friendly
- [ ] Focus management correct

### Code Style
- [ ] Consistent formatting
- [ ] Linter passing
- [ ] TypeScript types correct
- [ ] No `any` types
- [ ] No TODO comments without tickets

### Documentation
- [ ] Public APIs documented
- [ ] README updated if needed
- [ ] Complex logic explained
- [ ] Breaking changes noted
- [ ] Migration guide if breaking

## Common Issues to Flag

### Anti-Patterns
```javascript
// ❌ Magic numbers
if (status === 200) { }

// ✅ Named constants
const HTTP_OK = 200
if (status === HTTP_OK) { }

// ❌ Nested ternaries
const result = a ? b ? c : d : e

// ✅ If-else or early return
if (!a) return e
return b ? c : d

// ❌ Large objects in state
const [user, setUser] = useState(bigObject)

// ✅ Only store what's needed
const [userId, setUserId] = useState(id)
```

### Security Anti-Patterns
```javascript
// ❌ String concatenation in queries
db.query(`SELECT * FROM users WHERE id = ${id}`)

// ✅ Parameterized queries
db.query('SELECT * FROM users WHERE id = ?', [id])

// ❌ Eval with user input
eval(userCode)

// ✅ Don't use eval at all

// ❌ innerHTML with user data
div.innerHTML = userComment

// ✅ textContent or framework escaping
div.textContent = userComment
```

### Performance Anti-Patterns
```javascript
// ❌ Filter then map
items.filter(x => x.active).map(x => x.name)

// ✅ Reduce or single pass
items.reduce((acc, x) => {
  if (x.active) acc.push(x.name)
  return acc
}, [])

// ❌ useEffect without dependencies
useEffect(() => fetchData())

// ✅ Specify dependencies
useEffect(() => fetchData(), [userId])
```

## Review Priority Levels

### CRITICAL (Must Fix)
- Security vulnerabilities
- Data corruption risks
- Breaking changes without migration
- Secrets exposure
- Authentication bypass
- SQL injection

### HIGH (Should Fix)
- Performance bottlenecks
- Missing tests for new features
- Incorrect error handling
- Large functions (>50 lines)
- Deep nesting (>4 levels)
- Mutation patterns

### MEDIUM (Consider Fixing)
- Style inconsistencies
- Missing documentation
- Optimization opportunities
- TODO without tickets
- Minor accessibility issues

### LOW (Nice to Have)
- Refactoring suggestions
- Additional examples
- Minor improvements
- Naming improvements

## Review Output Format

For each issue found:

```
[PRIORITY] Brief description
File: path/to/file.ts:line
Issue: Detailed explanation
Fix: How to resolve

// ❌ Current code
// ✅ Suggested code
```

**Example**:
```
[CRITICAL] Hardcoded API key
File: src/api/client.ts:42
Issue: API key exposed in source code
Fix: Move to environment variable

const apiKey = "sk-abc123"  // ❌ Bad
const apiKey = process.env.OPENAI_API_KEY  // ✅ Good
```

## Approval Criteria

- ✅ **Approve**: No CRITICAL or HIGH issues
- ⚠️ **Warning**: Only MEDIUM issues (can merge with caution)
- ❌ **Block**: CRITICAL or HIGH issues found

## When to Use This Checklist

- ✅ After writing new code
- ✅ Before creating pull request
- ✅ During code review
- ✅ After bug fixes
- ✅ After refactoring
- ✅ Before merging to main

## Review Workflow

1. **Quick scan**
   - Run linter
   - Check for obvious issues
   - Verify tests pass

2. **Security review**
   - Check for secrets
   - Validate input handling
   - Verify auth/authorization

3. **Quality review**
   - Function sizes
   - Code complexity
   - Error handling

4. **Test review**
   - Coverage adequate
   - Edge cases tested
   - Tests passing

5. **Performance review**
   - Algorithms efficient
   - No obvious bottlenecks
   - Database queries optimized

## Tools to Use

### Linting
```bash
npm run lint
npx eslint . --fix
```

### Type Checking
```bash
npm run typecheck
npx tsc --noEmit
```

### Testing
```bash
npm test
npm run test:coverage
```

### Security
```bash
npm audit
npx eslint . --plugin security
```

### Bundle Size
```bash
npm run build
npx bundle-analyzer
```

## Common Mistakes

1. **Forgetting to validate input**
2. **Hardcoding configuration**
3. **Not handling errors**
4. **Writing tests after code** (should be TDD)
5. **Creating large files/functions**
6. **Using mutation instead of immutability**
7. **Leaving console.log statements**
8. **Not updating documentation**
9. **Ignoring accessibility**
10. **Adding unnecessary dependencies**

## Quick Reference

### Good Practices Checklist
- [ ] KISS (Keep It Simple)
- [ ] DRY (Don't Repeat Yourself)
- [ ] YAGNI (You Aren't Gonna Need It)
- [ ] Single Responsibility
- [ ] Small Functions & Files
- [ ] Immutability
- [ ] Error Handling
- [ ] Input Validation
- [ ] Test Coverage
- [ ] Clear Naming

### File Size Guidelines
- **Typical**: 200-400 lines
- **Maximum**: 800 lines
- **If larger**: Extract utilities, split by concern

### Function Size Guidelines
- **Typical**: 10-20 lines
- **Maximum**: 50 lines
- **If larger**: Extract helper functions

### Nesting Guidelines
- **Maximum**: 4 levels
- **If deeper**: Extract functions, use early returns

## Resources

- [Clean Code](https://github.com/ryanmcdermott/clean-code-javascript)
- [Airbnb JavaScript Style Guide](https://github.com/airbnb/javascript)
- [OWASP Top 10](https://owasp.org/www-project-top-ten/)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hellanglez) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
