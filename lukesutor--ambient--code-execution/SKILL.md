---
name: code-execution
description: Use when working with the Python code to execute
metadata:
  author: lukesutor
---

# Code Execution Skill

This skill allows running Python code in a sandboxed embedded environment. 

## Capabilities
- **Pure Python Logic**: Run algorithms, mathematical calculations, and string processing.
- **Sandboxed**: No access to file system or network.
- **State**: Execution is stateless (variables do not persist between calls).

## Limitations
- No standard library I/O (`os`, `sys`, `io`, `socket` are not available).
- No external packages (`numpy`, `pandas`, `requests` are not available).
- Only `print()` values are captured. Print any values you want to view.

## Guidelines
- Prefer iteration over recursion
- Prefer efficiency over readability

## Examples

### Calculate Fibonacci
```python
def fib(n):
    a, b = 0, 1
    for i in range(0, n):
        a, b = b, a + b
    return a
print(fib(10))
```

### Text Processing
```python
text = "hello world"
print(text.upper()[::-1])
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/lukesutor) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
