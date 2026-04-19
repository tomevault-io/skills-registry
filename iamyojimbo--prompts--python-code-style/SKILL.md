---
name: python-code-style
description: This skill should be used when writing, editing, or reviewing any Python code. It provides coding standards for comments, functions, error handling, code organization, naming conventions, type hints, return types, and logging practices. Always apply these guidelines when working with Python files (.py) or writing Python code snippets. Use when this capability is needed.
metadata:
  author: iamyojimbo
---

# Python Code Style

This skill provides comprehensive Python coding standards that must be followed
when writing any Python code. The standards emphasize clean code through
well-named functions, early failure principles, type safety with pydantic
models, and structured logging.

After writing code, you MUST note the code rules that you have applied to the
code, i.e. 1.1: No code comments.

## When to Use This Skill

Apply these coding standards whenever:

- Writing new Python code
- Editing existing Python files
- Reviewing Python code
- Creating Python code snippets or examples

## Rules

### 1. Apply Minimum Possible Change

1. When writing code, if you see existing code that violates this coding
   guideline, do not change it unless explicitly told to do so. This is to avoid
   changing too many things at once.

### 2. Code Comments

1. Do not write code comments. Separate code into clear logical blocks, and if
   we need to name such a block, put it in a well named function
2. Do not write docstrings
3. Keep in code comments that are in a plan that are indicating where new code
   should go and are not meant to persist in the final source code.

### 3. Functions

1. Put private functions at the bottom of the file
2. Avoid nesting functions within other functions
3. Default functions to private. Make them public only when used by another
   module.
4. Prefix private functions with an \_ as per python convention
5. Nested functions should not have the \_ prefix

### 4. Error handling

You must follow this diligently when writing all code:

1. The most important philosophy to follow is: fail as early as possible in code
   execution
2. Let the stack traces do the talking. Avoid wrapping exceptions
3. Never implement default handling unless explicitly instructed (i.e. using
   dict.get() unless it's completely core to the algorithm. We typically want to
   know if there's no data present).

### 5. Code Organisation

1. Place all source files in the `src/` dir
2. Always use relative imports

### 6. Naming

1. Prefer functional programming naming conventions names like:
   `name_frequency()` vs `get_name_frequency()`.
2. Use CamelCase for acronyms in class names, treating them as words:
   `DataForSeoTaskClient` vs `DataForSEOTaskClient`

### 7. Type Hints

1. Always add typehints to function signatures
2. Omit None from function return types by default, unless it's not clear
   otherwise.
3. Use built-in type hints (e.g., `list`, `dict`, `tuple`) instead of importing
   from typing module
4. Prefer `list[str]` over `List[str]` from typing
5. Prefer `dict[str, int]` over `Dict[str, int]` from typing
6. Never use invalid types in function signatures for None:
   - Bad: `value: int = None`
   - Good: `value: Optional[int] = None`
7. Prefer returning a pydantic type instead of a confusing tuple:
   - Bad: `def fn() -> tuple[list[int], list[int], list[int]]`
   - Good: `def fn(): FnResult`, where
     ```py
     class FnResult:
       user_ids: list[int]
       result_ids: list[int]
       other_ids: list[int]
     ```

8. Do not type with just `dict`. Use a pydantic type with clear field names.
   - Bad: `def fn(record: dict) -> dict`
   - Good: `def fn(record: Record): FnResult`
9. Prefer pydantic BaseModels to python @dataclasses for new code

### 8. Return Types

1. **Use pydantic BaseModel over complex dictionaries** for return types
   containing more than one field
2. Use pydantic BaseModel types for type safety, better IDE support, and clearer
   interfaces
3. Example:

```python
# Good: type-safe and clear
class SearchResult(BaseModel):
    query: str
    position: Optional[int]
    total_results: int

def search(query: str) -> SearchResult:
    return SearchResult(query=query, position=5, total_results=100)

# Bad: complex dicts are error-prone
def search(query: str) -> dict:
    return {"query": query, "position": 5, "total_results": 100}

# Bad: tuples are confusing when passed around
def search(query: str) -> tuple[str, int, int]:
    return query, 5, 100
```

### 9. Logging

1. All code should include logging
2. It should verify that code is working as expected
3. It should be placed so that, when issues arise, we can quickly pinpoint the
   problem, but not so many that we cannot read the code.
4. Use %s with arguments, vs f strings when logging, i.e.
   `log.info("Upload File: status=started id=%s, name=%s", id name)`
5. Use the format:

```text
Upload File: status=started id=1, name=test.csv
Upload File: status=done id=1, name=test.csv
Upload File: status=error id=1, name=test.csv, reason=timed_out, message=timed out after 5000ms
```

### 2.10. Pydantic

1. **ALWAYS use TypeAdapter when converting `list[dict]` to `list[Model]`**

   Whenever you have a list of raw data (dicts from API responses, cache
   entries, database rows, etc.) and need to convert to a list of pydantic
   models, use TypeAdapter to deserialize in one operation:

   ```py
   from pydantic import TypeAdapter

   # Good: TypeAdapter deserializes all items in one call
   accounts = TypeAdapter(list[GoogleAdsAccount]).validate_python(accounts_raw)

   # Bad: List comprehension with model_validate
   accounts = [GoogleAdsAccount.model_validate(_) for _ in accounts_raw]

   # Very Bad: Manual loop
   accounts = []
   for item in accounts_raw:
       accounts.append(GoogleAdsAccount.model_validate(item))
   ```

   **Key points:**
   - Use `.validate_python()` method (NOT `.model_validate()`)
   - Applies to: API responses, cache entries, DB query results, file parsing
   - More performant and cleaner than loops or list comprehensions

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/iamyojimbo) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
