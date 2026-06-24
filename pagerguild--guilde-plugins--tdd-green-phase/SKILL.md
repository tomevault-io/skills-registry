---
name: tdd-green-phase
description: | Use when this capability is needed.
metadata:
  author: pagerguild
---

# TDD Green Phase: Make Tests Pass

You are in the **GREEN** phase of Test-Driven Development.

## Your Goal

Write the **minimal code** necessary to make the failing test pass.

## Green Phase Rules

1. **Minimal Code**: Write ONLY enough code to pass the test
2. **No Extras**: Don't add features not required by current tests
3. **No Optimization**: Premature optimization is the enemy
4. **Stay Focused**: One test at a time

## The Minimal Implementation Mindset

Ask yourself:
- What is the SIMPLEST code that makes this test pass?
- Am I adding anything the test doesn't require?
- Can I hardcode a value to pass? (That's OK for now!)

## Example: Minimal vs Over-Engineered

**Test:**
```python
def test_add_returns_sum():
    assert add(2, 3) == 5
```

**Minimal (Good for GREEN):**
```python
def add(a, b):
    return a + b
```

**Over-Engineered (Bad for GREEN):**
```python
def add(a, b, precision=2, allow_negative=True, logging=False):
    """Add two numbers with extensive options."""
    if logging:
        logger.info(f"Adding {a} + {b}")
    result = a + b
    if not allow_negative and result < 0:
        raise ValueError("Negative result not allowed")
    return round(result, precision)
```

## Workflow

1. **Review the failing test** - understand what it expects
2. **Write minimal code** - just enough to pass
3. **Run the test** - verify it passes (green)
4. **Commit if needed** - small, passing commits are good

## Commands

```bash
# Verify you're in GREEN phase
bash scripts/tdd-enforcer.sh phase

# Run tests (expect pass)
bash scripts/tdd-enforcer.sh run <file>

# Check test coverage
bash scripts/tdd-enforcer.sh coverage
```

## After Green Phase

Once your test **passes**:

```bash
# Move to REFACTOR phase
bash scripts/tdd-enforcer.sh phase refactor
```

Then improve the code quality without changing behavior.

## It's OK to...

- Return hardcoded values if tests don't require more
- Use simple if/else instead of patterns
- Skip error handling not tested yet
- Write "ugly" code that works

These will be improved in the REFACTOR phase!

## Common Mistakes to Avoid

- Adding error handling not required by tests
- Generalizing beyond current requirements
- Adding logging, metrics, or observability
- Premature abstraction
- Writing more tests (that's RED phase again)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/pagerguild) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
