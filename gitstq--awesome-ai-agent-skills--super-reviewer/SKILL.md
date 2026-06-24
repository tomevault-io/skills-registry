---
name: super-reviewer
description: | Use when this capability is needed.
metadata:
  author: gitstq
---

# Super Reviewer - AI Senior Code Review Engine

> The most comprehensive AI code review skill. One skill replaces 10+ specialized review tools.

## Why This Skill?

Most code review skills only check one dimension (style or security). **Super Reviewer** performs a **7-dimensional holistic review** that mimics how a senior staff engineer reviews code in production:

1. **Correctness** - Logic bugs, null/undefined handling, edge cases, race conditions
2. **Security** - OWASP Top 10, injection attacks, auth bypasses, data exposure
3. **Performance** - N+1 queries, unnecessary re-renders, memory leaks, algorithm complexity
4. **Code Style** - Naming conventions, consistency, readability, DRY principle
5. **Architecture** - SOLID principles, coupling/cohesion, design patterns
6. **Testing** - Test coverage gaps, missing edge case tests, test quality
7. **Accessibility** - WCAG 2.1 AA compliance, keyboard navigation, screen reader support

## Quick Start

No setup needed. Simply say any of:
- "Review this code/PR"
- "Check this file for issues"
- "Do a security audit on..."
- "Review this code for performance"

The skill auto-detects language and framework.

## Review Process

### Phase 1: Context Analysis
1. Read all changed files in the PR/diff
2. Detect programming languages and frameworks used
3. Identify affected modules, APIs, and data flows
4. Check for existing tests related to changes

### Phase 2: Multi-Dimensional Scan
For each changed file, run ALL of the following checks:

#### 2.1 Correctness Check
- Null/undefined dereferences and missing null checks
- Off-by-one errors in loops and array indexing
- Unhandled promise rejections and async/await errors
- Incorrect boolean comparisons (== vs ===, assignment in conditions)
- Missing return statements in conditional branches
- Floating point precision issues
- String encoding problems
- Date/time zone handling errors
- Integer overflow in mathematical operations

#### 2.2 Security Check (OWASP-aligned)
- **Injection**: SQL injection, NoSQL injection, command injection, XSS, template injection
- **Auth**: Hardcoded credentials, missing auth checks, insecure token handling
- **Data**: Sensitive data in logs, PII exposure, insecure data storage
- **Crypto**: Weak hash algorithms, missing salt, predictable random values
- **Network**: Missing rate limiting, CORS misconfiguration, open redirects
- **Dependencies**: Known vulnerable packages, outdated dependencies
- **File ops**: Path traversal, insecure file uploads, directory listing

#### 2.3 Performance Check
- **Database**: N+1 queries, missing indexes hints, unnecessary JOINs
- **Frontend**: Unnecessary re-renders, missing memoization, large bundle imports
- **Memory**: Memory leaks in closures, event listeners, large object retention
- **Algorithm**: O(n^2) where O(n) possible, unnecessary sorting, redundant iterations
- **I/O**: Missing pagination, no streaming for large files, synchronous blocking calls
- **Caching**: Missing cache for expensive operations, stale cache, cache stampede

#### 2.4 Code Style Check
- Naming conventions per language ecosystem (camelCase, snake_case, PascalCase)
- File and folder organization consistency
- Import ordering and unused imports
- Function/method length (flag > 50 lines)
- Cyclomatic complexity (flag > 10)
- Magic numbers and strings (suggest constants/enums)
- TODO/FIXME/HACK comments tracking

#### 2.5 Architecture Check
- SOLID principle violations
- God objects/classes (flag > 500 lines, > 20 methods)
- Circular dependencies
- Leaky abstractions
- Missing dependency injection
- Tight coupling between modules
- Violation of separation of concerns

#### 2.6 Testing Check
- Changed code without corresponding test changes
- Missing edge case tests identified in correctness check
- Test assertions that are too broad (e.g., `expect(true).toBe(true)`)
- Missing negative test cases
- Test coverage gaps in critical paths

#### 2.7 Accessibility Check (for UI code only)
- Missing alt text on images
- Inadequate color contrast ratios
- Missing ARIA labels on interactive elements
- Keyboard navigation gaps
- Improper heading hierarchy
- Missing form labels and error messages
- Screen reader compatibility issues

### Phase 3: Report Generation

Generate a structured review report using the following format:

```
## Code Review Report

### Summary
- Files reviewed: X
- Critical issues: X | Warnings: X | Suggestions: X
- Overall assessment: [APPROVE / REQUEST_CHANGES / COMMENT]

### Critical Issues (Must Fix)

#### [SEC-001] SQL Injection Vulnerability (Line 42)
**File**: `src/api/users.ts`
**Severity**: CRITICAL
**Description**: User input `req.body.name` is directly interpolated into SQL query without sanitization.
**Impact**: An attacker could read, modify, or delete any data in the database.
**Fix**:
```typescript
// Before (vulnerable)
const query = `SELECT * FROM users WHERE name = '${req.body.name}'`;
// After (safe)
const query = 'SELECT * FROM users WHERE name = ?';
db.query(query, [req.body.name]);
```

#### [PERF-001] N+1 Query Problem (Line 78-85)
**File**: `src/services/order.ts`
**Severity**: WARNING
**Description**: Inside a loop, individual queries fetch user data for each order.
**Impact**: With 1000 orders, this generates 1000+ database queries instead of 1.
**Fix**: Use JOIN or batch loading with `IN` clause.

### Warnings (Should Fix)
...

### Suggestions (Nice to Have)
...

### Positive Highlights
- Good error handling in `src/utils/validation.ts`
- Proper use of TypeScript generics in repository pattern
- Comprehensive test coverage for payment module (95%)
```

### Phase 4: Severity Classification

Use these strict severity levels:

| Level | Color | Meaning | Action Required |
|-------|-------|---------|----------------|
| **CRITICAL** | Red | Security vulnerability, data loss risk, crash bug | MUST fix before merge |
| **WARNING** | Yellow | Performance issue, potential bug, bad practice | SHOULD fix before merge |
| **INFO** | Blue | Style improvement, readability suggestion | Consider fixing |

### Phase 5: Quick Fix Suggestions

For each issue, always provide:
1. **The problematic code** (exact line reference)
2. **Why it's a problem** (clear explanation)
3. **The fixed code** (copy-paste ready)
4. **Prevention tip** (how to avoid in the future)

## Framework-Specific Rules

### React / Next.js
- Check for missing `key` props in lists
- Detect stale closure bugs in `useEffect`
- Flag missing dependency array entries
- Check for prop drilling (suggest Context or Zustand)
- Verify `useMemo`/`useCallback` usage for expensive operations
- Check Server Component vs Client Component boundaries

### Vue / Nuxt
- Check reactive data declaration (`ref` vs `reactive` vs `computed`)
- Detect `v-if` + `v-for` on same element (anti-pattern)
- Check for proper cleanup of watchers and event listeners
- Verify composables follow naming convention (`use*`)
- Check Pinia store mutations pattern

### Node.js / Express
- Check for proper error middleware usage
- Detect missing input validation
- Check for proper async error handling
- Verify rate limiting on sensitive endpoints
- Check for helmet, cors security headers

### Python / Django / FastAPI
- Check for SQL injection in ORM raw queries
- Detect missing migration files
- Check for proper serializer validation
- Verify middleware ordering
- Check for proper virtual environment usage

### Go
- Check for proper error handling (no bare `err` ignores)
- Detect goroutine leaks
- Check for context propagation
- Verify mutex usage patterns
- Check for proper defer/panic/recover usage

## Anti-Patterns Database

The skill maintains an internal database of 200+ known anti-patterns across languages, including:

- **JavaScript**: Callback hell, implicit type coercion, prototype pollution
- **TypeScript**: Any abuse, type assertion overuse, missing return types
- **Python**: Mutable default arguments, global state, bare except
- **Java**: God class, feature envy, data class without equals/hashCode
- **Go**: Global mutable state, goroutine leak, interface pollution
- **Rust**: Unnecessary unwrap, unsafe block abuse, cloning large types

## Output Control

By default, generate a full report. User can request:
- `"brief review"` - Only critical and warning issues
- `"security only"` - Only security dimension
- `"performance only"` - Only performance dimension
- `"explain like I'm junior"` - Simpler explanations with more context

## Integration Notes

This skill works with any AI coding agent that supports the SKILL.md standard:
- Claude Code, Codex CLI, Cursor, Windsurf, GitHub Copilot
- CodeBuddy, OpenClaw, and any compatible agent
- No external dependencies required - all rules are embedded in this skill

---
> Source: [gitstq/awesome-ai-agent-skills](https://github.com/gitstq/awesome-ai-agent-skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-10 -->
