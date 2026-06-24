---
name: maverick-python-performance
description: Python performance optimization and profiling Use when this capability is needed.
metadata:
  author: get2knowio
---

# Python Performance Skill

Performance optimization patterns and profiling techniques.

## List Comprehensions vs Loops

```python
# FAST - list comprehension
squares = [x**2 for x in range(1000)]

# SLOW - append in loop
squares = []
for x in range(1000):
    squares.append(x**2)
```

## Generator Expressions (Lazy Evaluation)

```python
# Memory efficient for large datasets
squares = (x**2 for x in range(1_000_000))

# Only compute when needed
for square in squares:
    if square > 1000:
        break
```

## String Concatenation

```python
# BAD - O(n²) due to string immutability
result = ""
for item in items:
    result += str(item) + ","

# GOOD - O(n)
result = ",".join(str(item) for item in items)
```

## Dictionary Lookups

```python
# Use dict.get() with default
value = d.get(key, default_value)

# Use defaultdict for accumulation
from collections import defaultdict
counts = defaultdict(int)
for item in items:
    counts[item] += 1
```

## Profiling

```python
import cProfile
import pstats

cProfile.run('my_function()', 'output.prof')
stats = pstats.Stats('output.prof')
stats.sort_stats('cumulative').print_stats(10)
```

## Review Severity

- **MAJOR**: O(n²) string concatenation in loop
- **MINOR**: List when generator would suffice
- **SUGGESTION**: Could use comprehension instead of loop

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/get2knowio) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
