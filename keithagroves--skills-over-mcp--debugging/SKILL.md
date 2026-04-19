---
name: debugging
description: Systematic approach to debugging software issues. Use when encountering bugs, errors, unexpected behavior, or when trying to understand why code isn't working as expected. Use when this capability is needed.
metadata:
  author: keithagroves
---

# Debugging Skill

This skill provides a systematic methodology for debugging software issues.

## When to Use

- Application crashes or throws errors
- Unexpected behavior or wrong output
- Performance issues or slowdowns
- Intermittent failures
- Understanding unfamiliar code behavior

## Debugging Methodology

### 1. Reproduce the Issue

Before debugging, ensure you can reliably reproduce the problem:
- Document exact steps to reproduce
- Note the environment (OS, versions, config)
- Identify if it's consistent or intermittent

### 2. Gather Information

Collect relevant data:
- Error messages and stack traces
- Log files
- System state at time of failure
- Recent changes to the codebase

### 3. Form a Hypothesis

Based on the information:
- What component is likely failing?
- What could cause this specific behavior?
- What assumptions might be wrong?

### 4. Test the Hypothesis

Validate your theory:
- Add logging or print statements
- Use a debugger to inspect state
- Write a minimal test case
- Check boundary conditions

### 5. Fix and Verify

Once you find the root cause:
- Implement the fix
- Verify the original issue is resolved
- Check for regressions
- Add tests to prevent recurrence

## Common Debugging Techniques

### Binary Search Debugging

When you have a large codebase or long history:
1. Find a known good state
2. Find the bad state
3. Check the middle point
4. Repeat until you isolate the change

### Rubber Duck Debugging

Explain the problem out loud:
1. Describe what the code should do
2. Walk through what it actually does
3. The discrepancy often reveals the bug

### Print/Log Debugging

Strategic logging:
```python
print(f"DEBUG: variable={variable}, type={type(variable)}")
print(f"DEBUG: entering function with args={args}")
print(f"DEBUG: condition result={condition}")
```

### Divide and Conquer

1. Comment out half the code
2. Does the problem persist?
3. If yes, bug is in remaining code
4. If no, bug is in commented code
5. Repeat

## Common Bug Patterns

| Pattern | Symptoms | Likely Cause |
|---------|----------|--------------|
| Off-by-one | Wrong count, missing item | Loop bounds, index errors |
| Null reference | Crash on access | Uninitialized or missing data |
| Race condition | Intermittent failures | Concurrent access, timing |
| Memory leak | Gradual slowdown | Unreleased resources |
| Type coercion | Wrong values | Implicit conversions |

## Questions to Ask

- What changed recently?
- Does it fail in all environments?
- What are the exact inputs that cause failure?
- What does the error message actually say?
- Have you checked the obvious things?

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/keithagroves) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
