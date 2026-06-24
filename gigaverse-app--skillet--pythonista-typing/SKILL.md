---
name: pythonista-typing
description: Use when adding type hints, fixing type checker errors, working with Pydantic models. Triggers on "type", "types", "typing", "Pydantic", "BaseModel", "pyright", "basedpyright", "mypy", "type error", "type hint", "annotation", "Any", "dict", "list", "Optional", "Cannot assign", "Incompatible types", "Missing return type", "reportArgumentType", or when defining function signatures or data models.
metadata:
  author: gigaverse-app
---

# Type Annotations and Pydantic Best Practices

## Core Philosophy

**Use Pydantic models for structured data. Use specific types everywhere. Never use `Any` or raw dicts when structure is known.**

## Quick Start - Fixing Type Errors

```bash
# 1. Run type checker
pyright <file-or-directory>
# or
basedpyright <file-or-directory>

# 2. Fix errors (see patterns below and reference files)

# 3. Verify no behavior changes
pytest tests/
```

## Fix Priority Order

1. **Add proper type annotations** (Optional, specific types)
2. **Fix decorator return types**
3. **Use `cast()`** for runtime-compatible but statically unverifiable types
4. **Last resort: `# type: ignore`** only for legitimate cases

## Type Annotation Rules

### Modern Python Syntax (3.9+)

```python
# CORRECT
def process_data(items: list[str]) -> dict[str, list[int]]:
    results: dict[str, list[int]] = {}
    return results

# WRONG - Old style
from typing import Dict, List
def process_data(items: List[str]) -> Dict[str, List[int]]:
    ...
```

### NEVER Use Weak Types

```python
# NEVER
items: list[Any]
data: dict
result: Any

# ALWAYS use specific types
items: list[DataItem]
data: dict[str, ProcessedResult]
result: SpecificType | OtherType
```

### NEVER Use hasattr/getattr as Type Substitutes

```python
# WRONG - Type cop-out
def process(obj: Any):
    if hasattr(obj, "name"):
        return obj.name

# CORRECT - Use Protocol
class Named(Protocol):
    name: str

def process(obj: Named) -> str:
    return obj.name
```

### Complex Return Types Must Be Named

```python
# WRONG - Unreadable
def execute() -> tuple[BatchResults, dict[str, Optional[Egress]]]:
    pass

# CORRECT - Named model
class ExecutionResult(BaseModel):
    batch_results: BatchResults
    egress_statuses: dict[str, Optional[Egress]]

def execute() -> ExecutionResult:
    pass
```

**Rule**: If you can't read the type annotation out loud in one breath, it needs a named model.

## Pydantic Rules

### Always Use Pydantic Models for Structured Data

```python
# WRONG - Raw dict
def get_result() -> dict[str, Any]:
    return {"is_valid": True, "score": 0.95}

# CORRECT - Pydantic model
class Result(BaseModel):
    is_valid: bool
    score: float

def get_result() -> Result:
    return Result(is_valid=True, score=0.95)
```

### TypedDict and dataclasses Are Prohibited

**NEVER use TypedDict or dataclasses without explicit authorization. Always use Pydantic.**

### Never Convert Models to Dicts Just to Add Fields

```python
# WRONG - Breaking type safety
result_dict = result.model_dump()
result_dict["_run_id"] = run_id  # Now untyped!

# CORRECT - Extend the model
class ResultWithRunId(BaseModel):
    details: ResultDetails
    run_id: str | None = None
```

## Prefer cast() Over type: ignore

```python
from typing import cast

# Preferred
typed_results = cast(list[ResultProtocol], results)
selected = select_by_score(typed_results)

# Less clear
selected = select_by_score(results)  # type: ignore[arg-type]
```

## When to Use type: ignore

Only for:
1. Function attributes: `func._attr = val  # type: ignore[attr-defined]`
2. Dynamic/runtime attributes not in type system
3. External library quirks (protobuf, webhooks)
4. Legacy patterns requiring significant refactoring

DO NOT use for simple fixes (add Optional, fix return types).

## Reference Files

For detailed patterns:
- [references/pydantic-patterns.md](references/pydantic-patterns.md) - Pydantic patterns and external system integration
- [references/expanding-coverage.md](references/expanding-coverage.md) - How to add new modules to type checking

## Related Skills

- [/pythonista-testing](../pythonista-testing/SKILL.md) - Testing typed code
- [/pythonista-reviewing](../pythonista-reviewing/SKILL.md) - Type issues in code review
- [/pythonista-async](../pythonista-async/SKILL.md) - Async type patterns

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/gigaverse-app) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
