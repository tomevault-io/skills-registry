---
name: sentry-find-bugs
description: Find bugs and security vulnerabilities in code changes. Use when analyzing branch changes, reviewing diffs, or hunting for defects. Use when this capability is needed.
metadata:
  author: allanninal
---

# Bug Finding

## When to Use This Skill

- Analyzing code changes for bugs
- Reviewing diffs before merge
- Hunting for security vulnerabilities
- Investigating reported issues

## Bug Categories

### 1. Logic Errors

```markdown
## Common Logic Bugs

### Off-by-One Errors
```javascript
// BUG: < should be <=
for (let i = 0; i < items.length - 1; i++) { }

// BUG: Wrong boundary
if (index > array.length) { } // Should be >=
```

### Incorrect Conditionals
```javascript
// BUG: && should be ||
if (user.isAdmin && user.isSuperUser) { } // Both required, but intended either

// BUG: Inverted logic
if (!isValid) {
  proceed(); // Should only proceed if valid
}
```

### State Management
```javascript
// BUG: Stale closure
useEffect(() => {
  setInterval(() => {
    setCount(count + 1); // count is stale
  }, 1000);
}, []);

// FIX: Use functional update
setCount(prev => prev + 1);
```
```

### 2. Null/Undefined Errors

```markdown
## Null Safety Issues

### Missing Null Checks
```typescript
// BUG: user could be null
const name = user.name;

// FIX: Optional chaining
const name = user?.name;

// BUG: Assumes array has elements
const first = items[0].id;

// FIX: Check first
const first = items[0]?.id;
```

### Undefined Properties
```typescript
// BUG: Property might not exist
const value = config.settings.theme.color;

// FIX: Safe access
const value = config?.settings?.theme?.color ?? defaultColor;
```
```

### 3. Race Conditions

```markdown
## Concurrency Bugs

### Check-Then-Act
```python
# BUG: Race condition
if file_exists(path):
    # Another process could delete file here
    data = read_file(path)

# FIX: Handle exception
try:
    data = read_file(path)
except FileNotFoundError:
    data = None
```

### Shared State
```javascript
// BUG: Race condition with async
let counter = 0;
async function increment() {
  const current = counter;
  await doSomething();
  counter = current + 1; // Lost updates
}

// FIX: Atomic operation or lock
```
```

### 4. Resource Leaks

```markdown
## Resource Management

### Unclosed Resources
```python
# BUG: File not closed on error
file = open('data.txt')
data = process(file.read())  # If this throws, file stays open
file.close()

# FIX: Use context manager
with open('data.txt') as file:
    data = process(file.read())
```

### Event Listener Leaks
```javascript
// BUG: Listener never removed
useEffect(() => {
  window.addEventListener('resize', handler);
}, []);

// FIX: Cleanup function
useEffect(() => {
  window.addEventListener('resize', handler);
  return () => window.removeEventListener('resize', handler);
}, []);
```
```

### 5. Security Vulnerabilities

```markdown
## Security Bugs

### Injection
```python
# BUG: SQL injection
query = f"SELECT * FROM users WHERE id = {user_id}"

# FIX: Parameterized query
query = "SELECT * FROM users WHERE id = %s"
cursor.execute(query, (user_id,))
```

### XSS
```javascript
// BUG: XSS vulnerability
element.innerHTML = userInput;

// FIX: Use textContent
element.textContent = userInput;
```

### Path Traversal
```python
# BUG: Path traversal
path = f"/uploads/{filename}"

# FIX: Sanitize filename
safe_name = os.path.basename(filename)
path = os.path.join("/uploads", safe_name)
```
```

## Bug Detection Checklist

```markdown
## For Each Change, Check:

### Input Handling
- [ ] Are all inputs validated?
- [ ] Are edge cases handled (empty, null, max values)?
- [ ] Is user input sanitized before use?

### Error Handling
- [ ] Are exceptions caught appropriately?
- [ ] Do error handlers not swallow important info?
- [ ] Are resources cleaned up on error?

### State Management
- [ ] Is state updated atomically when needed?
- [ ] Are race conditions possible?
- [ ] Is there risk of stale data?

### Boundaries
- [ ] Are array accesses within bounds?
- [ ] Are loop conditions correct?
- [ ] Are numeric operations safe (overflow)?

### Security
- [ ] No hardcoded secrets?
- [ ] No injection vulnerabilities?
- [ ] Proper authentication/authorization?
```

## Analysis Approach

### Step 1: Understand the Change

```bash
# Get diff of changes
git diff main...HEAD

# See affected files
git diff main...HEAD --name-only

# Get context
git log main..HEAD --oneline
```

### Step 2: Trace Data Flow

```markdown
## Data Flow Analysis

1. **Input Sources**: Where does data enter?
   - User input
   - API responses
   - Database queries
   - File reads

2. **Processing**: How is data transformed?
   - Parsing
   - Validation
   - Computation

3. **Output Sinks**: Where does data go?
   - Database writes
   - API responses
   - File writes
   - UI rendering
```

### Step 3: Check Edge Cases

```markdown
## Edge Cases to Test

### Strings
- Empty string ""
- Very long string
- Special characters
- Unicode/emoji
- Whitespace only

### Numbers
- Zero
- Negative numbers
- Very large numbers
- Floating point precision

### Collections
- Empty array/object
- Single element
- Many elements
- Null values in collection

### Timing
- Concurrent requests
- Slow network
- Timeout scenarios
```

## Reporting Bugs

```markdown
## Bug Report Template

### Summary
One-line description of the bug

### Location
File: `src/services/user.ts`
Line: 45-52

### Description
[What the bug is and why it's a problem]

### Reproduction
1. Step one
2. Step two
3. Observe bug

### Impact
- [What could go wrong]
- [Who is affected]

### Suggested Fix
```code
[Fixed code]
```

### Severity
- 🔴 Critical (security/data loss)
- 🟠 High (major functionality broken)
- 🟡 Medium (degraded experience)
- 🟢 Low (minor/cosmetic)
```

## Tools for Bug Finding

```bash
# Static analysis
npx eslint --ext .ts,.tsx src/
npm run typecheck

# Security scanning
npx semgrep --config auto

# Find common issues
grep -r "eval(" src/
grep -r "innerHTML" src/
grep -r "dangerouslySetInnerHTML" src/
```

## Best Practices

- [ ] Review code change by change, not file by file
- [ ] Trace data from input to output
- [ ] Question assumptions
- [ ] Think adversarially
- [ ] Test edge cases mentally
- [ ] Check for similar bugs elsewhere
- [ ] Verify fixes don't introduce new bugs

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/allanninal) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
