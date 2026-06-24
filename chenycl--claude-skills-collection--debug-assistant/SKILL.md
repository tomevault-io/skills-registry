---
name: debug-assistant
description: > Use when this capability is needed.
metadata:
  author: chenycl
---

# Debug Assistant

Systematic debugging methodology for quickly identifying and resolving code issues.

## Debugging Framework

### 1. Understand the Problem
- What is the expected behavior?
- What is the actual behavior?
- When did it start failing?
- Is it reproducible?

### 2. Gather Information
- Full error message and stack trace
- Input data that triggers the issue
- Environment details (OS, runtime version, dependencies)
- Recent code changes

### 3. Form Hypotheses
- List possible causes
- Rank by likelihood
- Consider edge cases

### 4. Test Hypotheses
- Add logging/breakpoints
- Isolate the problem
- Binary search through code changes

### 5. Fix and Verify
- Implement fix
- Test the specific case
- Test related functionality
- Add regression test

## Common Error Patterns

### Null/Undefined Errors

**JavaScript:**
```javascript
// Error: Cannot read property 'x' of undefined
// Solution: Optional chaining or null check
const value = obj?.nested?.property ?? defaultValue;
```

**Python:**
```python
# AttributeError: 'NoneType' object has no attribute 'x'
# Solution: Check for None
value = obj.attr if obj is not None else default
```

### Type Errors

**TypeScript:**
```typescript
// Type 'string' is not assignable to type 'number'
// Solution: Type conversion or fix the type
const num: number = parseInt(stringValue, 10);
```

**Python:**
```python
# TypeError: unsupported operand type(s)
# Solution: Ensure consistent types
result = int(a) + int(b)
```

### Async/Await Issues

**JavaScript:**
```javascript
// Common: Forgetting await
// Wrong
const data = fetchData(); // Returns Promise, not data
// Correct
const data = await fetchData();

// Common: Parallel vs Sequential
// Sequential (slow)
const a = await fetchA();
const b = await fetchB();
// Parallel (fast)
const [a, b] = await Promise.all([fetchA(), fetchB()]);
```

**Python:**
```python
# Common: Mixing sync and async
# Wrong
def sync_function():
    await async_call()  # SyntaxError
# Correct
async def async_function():
    await async_call()
```

### Memory Issues

**Signs:**
- Gradually increasing memory usage
- OutOfMemoryError
- Process killed by OS

**Common causes:**
- Unbounded caches/collections
- Event listener not removed
- Circular references
- Large objects in closures

### Race Conditions

**Signs:**
- Intermittent failures
- Works in debugger, fails in production
- Different behavior under load

**Solutions:**
- Mutex/locks
- Atomic operations
- Message queues
- Idempotent operations

## Stack Trace Analysis

### JavaScript
```
TypeError: Cannot read property 'name' of undefined
    at getUserName (user.js:15:23)
    at processUser (handler.js:42:12)
    at main (index.js:8:5)
```
- Read bottom to top for call order
- Error location: `user.js` line 15, column 23
- Look for your code (not node_modules)

### Python
```
Traceback (most recent call last):
  File "main.py", line 8, in <module>
    main()
  File "handler.py", line 42, in process_user
    get_user_name(user)
  File "user.py", line 15, in get_user_name
    return user.name
AttributeError: 'NoneType' object has no attribute 'name'
```
- Read top to bottom for call order
- Most specific error at bottom

### Java
```
java.lang.NullPointerException
    at com.example.User.getName(User.java:15)
    at com.example.Handler.processUser(Handler.java:42)
    at com.example.Main.main(Main.java:8)
```
- Read top to bottom for location
- Check for "Caused by:" for root cause

## Debugging Commands

### Node.js
```bash
# Debug mode
node --inspect app.js
# Chrome DevTools: chrome://inspect

# Memory snapshot
node --inspect --expose-gc app.js
```

### Python
```bash
# PDB debugger
python -m pdb script.py

# In code
import pdb; pdb.set_trace()  # Python 3.6-
breakpoint()  # Python 3.7+
```

### General
```bash
# Verbose logging
DEBUG=* node app.js
RUST_LOG=debug cargo run
```

## Quick Fixes Checklist

- [ ] Restart the service/process
- [ ] Clear cache/rebuild
- [ ] Check environment variables
- [ ] Verify dependencies installed
- [ ] Check file permissions
- [ ] Review recent git commits
- [ ] Check logs for earlier errors
- [ ] Try in isolation (minimal reproduction)

## References

See [references/debugging_techniques.md](references/debugging_techniques.md) for advanced debugging strategies.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/chenycl) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
