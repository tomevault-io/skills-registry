---
name: refactoring
description: Safe refactoring workflow for improving code without changing behavior. Use when cleaning up code, reducing duplication, or improving structure. Use when this capability is needed.
metadata:
  author: wdonofrio
---

# Refactoring Workflow

Improve code structure without changing behavior. Safety comes from tests and small steps.

## When to Refactor

**Good times to refactor:**
- Before adding a new feature (make room for it)
- After getting something working (clean it up)
- When you find code hard to understand
- When fixing a bug reveals structural issues
- During code review feedback

**Bad times to refactor:**
- Mixed with feature work (separate commits)
- Without tests covering the code
- Under time pressure with no safety net
- Just because you'd write it differently

## Refactoring Safety

### Before Starting

1. **Ensure tests exist and pass**
   - Run the full test suite
   - If no tests exist, write characterization tests first
   - Know how to run tests quickly

2. **Understand the code**
   - Read it thoroughly before changing
   - Trace the execution paths
   - Identify all callers/usages

3. **Commit current state**
   - Start from a clean working tree
   - Easy to revert if things go wrong

### During Refactoring

1. **Small steps**
   - Each step should be trivially correct
   - Run tests after each step
   - Commit frequently

2. **One refactoring at a time**
   - Don't combine multiple improvements
   - Finish one before starting another

3. **No behavior changes**
   - Refactoring changes structure, not behavior
   - If you find a bug, note it and fix separately

### After Refactoring

1. **Run all tests**
   - Not just tests for changed code
   - Performance tests if relevant

2. **Review the diff**
   - Does it make sense as a whole?
   - Would someone else understand the improvement?

3. **Commit with clear message**
   - Explain what was improved and why

## Common Refactorings

### Extract Function/Method
**When:** Code block does one clear thing, or you need to add a comment explaining what it does.

```
# Before
def process():
    # validate input
    if not data:
        raise Error()
    if len(data) > MAX:
        raise Error()
    # ... rest of function

# After
def process():
    validate_input(data)
    # ... rest of function

def validate_input(data):
    if not data:
        raise Error()
    if len(data) > MAX:
        raise Error()
```

### Inline Function/Method
**When:** The function body is as clear as its name, or it's just delegation.

### Rename
**When:** Name doesn't clearly describe what something does.

- Variables, functions, classes, files
- Update all references
- IDE refactoring tools help

### Extract Variable
**When:** Expression is complex or used multiple times.

```
# Before
if user.subscription.plan.price > 100 and user.subscription.plan.price < 500:

# After
price = user.subscription.plan.price
if price > 100 and price < 500:
```

### Replace Conditional with Polymorphism
**When:** Switch/if-else on type determines behavior.

### Extract Class
**When:** Class does too many things or has feature envy.

### Move Function/Field
**When:** Function uses data from another class more than its own.

### Replace Magic Number with Named Constant
**When:** Numeric literal has meaning that's not obvious.

## Refactoring Patterns

### Strangler Fig
For large refactorings:
1. Create new implementation alongside old
2. Route some traffic to new implementation
3. Gradually migrate all usages
4. Remove old implementation

### Parallel Change (Expand-Contract)
For API changes:
1. Add new interface (expand)
2. Migrate all callers to new interface
3. Remove old interface (contract)

### Branch by Abstraction
For replacing dependencies:
1. Create abstraction over current implementation
2. Change all callers to use abstraction
3. Create new implementation of abstraction
4. Switch to new implementation
5. Remove abstraction if no longer needed

## Code Smells to Address

| Smell | Description | Refactoring |
|-------|-------------|-------------|
| Long function | Too much in one place | Extract functions |
| Duplicated code | Same code in multiple places | Extract and reuse |
| Long parameter list | Too many arguments | Introduce parameter object |
| Feature envy | Method uses another class's data heavily | Move method |
| Data clumps | Same fields always appear together | Extract class |
| Primitive obsession | Using primitives instead of small objects | Introduce value object |
| Switch statements | Type-based switches | Replace with polymorphism |
| Speculative generality | Unused abstractions | Inline/remove |

## Reminders

- Tests are your safety net—don't refactor without them
- Small steps, frequent commits
- Don't mix refactoring with feature changes
- Reference `shared/guidelines/coding-style.md` for style goals

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/wdonofrio) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
