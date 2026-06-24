---
name: tdd-property-testing
description: Using generative testing to explore large input spaces and find obscure bugs. Use when this capability is needed.
metadata:
  author: jcorpac
---

# Property-Based Testing

Instead of writing individual test cases with fixed inputs, you define **properties** (invariants) that should always be true for any valid input.

## How it Works
1. Define a strategy for generating random data (e.g., "any list of integers").
2. Define a property (e.g., "sorting a list doesn't change its length").
3. The framework generates hundreds of inputs to find a counter-example.
4. If it finds one, it **shrinks** the input to the simplest possible case that fails.

## Tools
- **Python**: [Hypothesis](https://hypothesis.readthedocs.io/)
- **JavaScript**: [fast-check](https://github.com/dubzzz/fast-check)
- **Rust**: [proptest](https://github.com/proptest-rs/proptest)

## Example (Python with Hypothesis)
```python
from hypothesis import given, strategies as st

def add(a, b):
    return a + b

@given(st.integers(), st.integers())
def test_add_commutative(a, b):
    assert add(a, b) == add(b, a)
```

## When to Use
- Mathematical functions.
- Encoders/Decoders.
- Data transformation pipelines.
- Sorting or searching algorithms.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jcorpac) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
