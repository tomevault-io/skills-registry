---
name: refactoring
description: Refactor code to improve maintainability by consolidating redundant logic and applying DRY principles Use when this capability is needed.
metadata:
  author: uuta
---

# Refactoring

冗長なロジックや重複している箇所を集約したりすることで保守性を上げる。

## Stragegy

- Check whether the logic can be erased by consolidating into Schema
- Test first. Before starting implementation, check the current tests for the target method cover the all pattern.

## Points of view

The following rules are listed by the order of priorities.

- Is the code in the right place from a DDD perspective?
- Is it possible to remove the logic by changing the schema itself?
- Isn't it against DRY?
- Is it possible to write code with better visibility by cutting it out into a separate method?
- Can loop be improved more? (O(n) > O(n^2) > O(2^n) > O(n!))

## Validate Assumptions Against Downstream Capabilities

Before adding preprocessing or normalization steps, verify that the downstream process doesn't already handle the problem.

**Ask:** "Does the downstream layer already have the capability to handle this without preprocessing?"

If the answer is yes, the upstream processing is redundant and should be removed.

**Examples:**
- Normalizing input to English before passing to an AI that already handles multilingual input
- Fetching a "cleaner" version of data when the downstream consumer can work with the original
- Transforming data into a format that the next step will transform again anyway

**Related smell:** A `try/catch` fallback that almost always fires is a signal that the `try` block's assumption is wrong — the "exception" has become the norm. Treat it as near-dead code.

## Fat Usecase Avoidance

Usecases should orchestrate operations, not contain business logic. Follow these principles to keep usecases thin and maintainable.

### 1. Move Logic to Appropriate Layers

Place logic where it belongs according to DDD principles. Business rules, complex calculations, and data transformations should live in domain services and value objects, not in usecases.

**Guidelines:**
- Business rules → Domain services
- Data transformation → Value objects
- Validation → Domain validators
- Complex calculations → Domain services

**Example:** Move filtering logic to domain services (e.g., `FileFilter.shouldReview()`), calculations to value objects (e.g., `ComplexityCalculator.calculate()`). Usecases only orchestrate these operations.

### 2. Eliminate else Clauses

Use early returns and guard clauses instead of else blocks to reduce cognitive load.

**Before:**
```
if condition:
    if another_condition:
        return success_result
    else:
        return failure
else:
    raise error
```

**After:**
```
if not condition:
    raise error
if not another_condition:
    return failure
return success_result
```

### 3. Reduce Nesting Depth

Flatten nested structures with early returns or by extracting methods. Replace multiple nested conditions with guard clauses that return early, or extract the nested logic into well-named helper methods.

### 4. When to Extract Helper Methods

Extract methods to improve single responsibility, not just to hide complexity.

**When to extract:**
- Logic has a clear single responsibility
- Method can be meaningfully named
- Logic might be reused
- Makes the main flow more readable

**When NOT to extract:**
- Just to reduce line count
- Creates methods that are only called once with unclear purpose
- Makes code harder to follow by breaking natural flow
- Hides complexity instead of addressing it

Avoid over-extraction that creates trivial helper methods. Simple operations like `getFiles()` that just returns `request.files` or `buildResponse()` that just wraps a constructor call should stay inline.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/uuta) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
