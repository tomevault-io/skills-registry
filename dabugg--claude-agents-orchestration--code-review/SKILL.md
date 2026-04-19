---
name: code-review
description: Reviews code for bugs, security issues, performance problems, and best practices. Activates when user asks for code review, feedback on code, or wants to improve existing code. Use when this capability is needed.
metadata:
  author: dabugg
---

# Code Review

## Overview
Performs comprehensive code reviews focusing on bugs, security vulnerabilities, performance issues, and adherence to best practices. Provides actionable feedback with specific suggestions and code examples.

## Auto-Activation Conditions
This skill activates when:
- ✅ User asks to review code or give feedback
- ✅ Request mentions finding bugs or issues
- ✅ User wants to improve or refactor code
- ✅ Security or performance review requested
- ✅ Keywords: review, bugs, improve, refactor, quality, security

Does NOT activate when:
- ❌ User wants to write new code from scratch → use code generation skill
- ❌ User needs documentation → use docs skill
- ❌ User wants tests written → use testing skill

## Instructions

### Phase 1: Understand the Code
Before reviewing, identify:
```python
context = {
    "language": "<detected programming language>",
    "purpose": "<what does this code do?>",
    "scope": "<function|class|module|full application>",
    "framework": "<if applicable: React, Django, etc.>",
    "review_focus": "<bugs|security|performance|all>"
}
```

### Phase 2: Systematic Review
Review the code in this order:

#### 1. Critical Issues (Fix Immediately)
- **Security vulnerabilities**
  - SQL injection
  - XSS vulnerabilities
  - Hardcoded secrets/credentials
  - Insecure data handling
  - Missing input validation

- **Bugs & Logic Errors**
  - Off-by-one errors
  - Null/undefined handling
  - Race conditions
  - Infinite loops
  - Wrong operator usage

#### 2. Performance Issues
- Unnecessary loops or iterations
- N+1 query problems
- Missing caching opportunities
- Memory leaks
- Inefficient algorithms

#### 3. Code Quality
- Naming conventions
- Function length (should be < 20 lines)
- Code duplication (DRY violations)
- Missing error handling
- Unclear logic flow

#### 4. Best Practices
- Language-specific idioms
- Framework conventions
- SOLID principles
- Type safety
- Documentation needs

### Phase 3: Provide Feedback

#### Feedback Format
For each issue found:
```markdown
### [SEVERITY] Issue Title

**Location**: `filename.py:line_number` or code snippet
**Category**: Security | Bug | Performance | Quality | Best Practice

**Problem**:
<Clear explanation of what's wrong>

**Impact**:
<What could happen if not fixed>

**Solution**:
```language
// Fixed code example
```

**Why this is better**:
<Brief explanation>
```

#### Severity Levels
| Level | Description | Action |
|-------|-------------|--------|
| 🔴 CRITICAL | Security hole, data loss risk | Fix immediately |
| 🟠 HIGH | Bug that causes incorrect behavior | Fix before merge |
| 🟡 MEDIUM | Performance or maintainability issue | Should fix soon |
| 🟢 LOW | Style or minor improvement | Nice to have |

### Phase 4: Summary & Recommendations

Always end with:
```markdown
## Review Summary

| Category | Issues Found |
|----------|-------------|
| 🔴 Critical | X |
| 🟠 High | X |
| 🟡 Medium | X |
| 🟢 Low | X |

## Top 3 Priority Fixes
1. <Most important fix>
2. <Second priority>
3. <Third priority>

## Overall Assessment
<Brief overall impression and recommendation>
```

## Review Patterns by Language

### Python
```python
# BAD: Mutable default argument
def add_item(item, items=[]):
    items.append(item)
    return items

# GOOD: Use None and create new list
def add_item(item, items=None):
    if items is None:
        items = []
    items.append(item)
    return items
```

### JavaScript/TypeScript
```javascript
// BAD: == instead of ===
if (value == null) { }

// GOOD: Strict equality
if (value === null || value === undefined) { }
// or
if (value == null) { } // Only case where == is acceptable
```

### SQL (in code)
```python
# BAD: SQL Injection vulnerability
query = f"SELECT * FROM users WHERE id = {user_id}"

# GOOD: Parameterized query
query = "SELECT * FROM users WHERE id = %s"
cursor.execute(query, (user_id,))
```

### React
```jsx
// BAD: Missing key in list
{items.map(item => <Item data={item} />)}

// GOOD: Unique key prop
{items.map(item => <Item key={item.id} data={item} />)}
```

## Examples

### Example 1: Python Function Review

**Input**:
```python
def get_user(id):
    conn = sqlite3.connect('db.sqlite')
    result = conn.execute(f"SELECT * FROM users WHERE id = {id}")
    return result.fetchone()
```

**Output**:
```markdown
### 🔴 CRITICAL: SQL Injection Vulnerability

**Location**: `get_user` function, line 3
**Category**: Security

**Problem**:
User input is directly interpolated into SQL query using f-string, allowing SQL injection attacks.

**Impact**:
Attacker could extract all data, delete tables, or gain unauthorized access by passing malicious input like `1; DROP TABLE users;--`

**Solution**:
```python
def get_user(id: int) -> Optional[tuple]:
    """Fetch user by ID from database."""
    conn = sqlite3.connect('db.sqlite')
    try:
        cursor = conn.execute(
            "SELECT * FROM users WHERE id = ?",
            (id,)
        )
        return cursor.fetchone()
    finally:
        conn.close()
```

**Why this is better**:
- Parameterized query prevents SQL injection
- Type hint documents expected input
- Connection properly closed with try/finally
- Docstring explains function purpose

---

### 🟠 HIGH: Resource Leak

**Location**: `get_user` function
**Category**: Bug

**Problem**:
Database connection is never closed, causing resource leaks.

**Solution**: See fixed code above with try/finally block.
```

### Example 2: React Component Review

**Input**:
```jsx
function UserList({users}) {
  const [filter, setFilter] = useState()

  const filtered = users.filter(u => u.name.includes(filter))

  return (
    <div>
      <input onChange={e => setFilter(e.target.value)} />
      {filtered.map(user => <UserCard user={user} />)}
    </div>
  )
}
```

**Output**:
```markdown
### 🟠 HIGH: Potential Runtime Error

**Location**: Line 4, filter operation
**Category**: Bug

**Problem**:
`filter` state is initialized as `undefined`. Calling `includes(undefined)` won't crash, but `filter` being undefined on first render may cause unexpected behavior.

**Solution**:
```jsx
const [filter, setFilter] = useState('')
```

---

### 🟡 MEDIUM: Missing Key Prop

**Location**: Line 9, map rendering
**Category**: Best Practice

**Problem**:
React list items missing unique `key` prop, causing inefficient re-renders.

**Solution**:
```jsx
{filtered.map(user => <UserCard key={user.id} user={user} />)}
```

---

### 🟡 MEDIUM: Performance - Unnecessary Recalculation

**Location**: Line 4
**Category**: Performance

**Problem**:
Filter runs on every render, even when users array hasn't changed.

**Solution**:
```jsx
const filtered = useMemo(
  () => users.filter(u => u.name.includes(filter)),
  [users, filter]
)
```
```

## Quality Checklist
Before completing review, verify:
- [ ] All code paths examined
- [ ] Security implications considered
- [ ] Edge cases identified
- [ ] Performance bottlenecks noted
- [ ] Fixes include code examples
- [ ] Severity levels assigned
- [ ] Summary provided
- [ ] Prioritized recommendations given

## Anti-Patterns in Reviews
- ❌ Vague feedback: "This could be better"
- ❌ No code examples for fixes
- ❌ Nitpicking style when there are real bugs
- ❌ Missing severity levels
- ❌ No explanation of WHY something is wrong
- ❌ Reviewing without understanding context

## Integration
- **Works with**: testing (suggest tests for bug-prone areas), documentation
- **Browser**: Not required

## Changelog
- v1.0.0 (2025-01-25): Initial creation

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/dabugg) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
