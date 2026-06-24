---
name: async-patterns
description: Use when writing async code, using asyncio.gather, handling concurrent operations, or when user mentions "asyncio", "async", "await", "gather", "concurrent", "parallel tasks", "fail-fast", "timeout", "race condition", "cancellation".
metadata:
  author: gigaverse-app
---

# Async Patterns

## Rules Summary

1. Prefer `safe_gather` for all new async code - it provides fail-fast, timeout support, and cleaner cancellation
2. Use `safe_gather` with `return_exceptions=True` for partial-results patterns (better than asyncio.gather due to timeout support)
3. Migrating existing cleanup/shutdown code to `safe_gather` is optional (low priority, both work fine)
4. Only use `asyncio.wait` with `FIRST_COMPLETED` for incremental processing patterns
5. Always consider timeout and cancellation behavior when choosing async primitives

---

## safe_gather vs asyncio.gather

### Default Choice: safe_gather

**`safe_gather` is better than `asyncio.gather` in ALL cases** because it provides:
- Fail-fast cancellation (when not using `return_exceptions=True`)
- Timeout support with automatic cleanup
- Cleaner cancellation handling
- Same behavior as `asyncio.gather` when using `return_exceptions=True`

### Implementation

If you don't have `safe_gather` in your codebase, here's a reference implementation:

```python
import asyncio
from typing import Any, Coroutine

async def safe_gather(
    *coros: Coroutine[Any, Any, Any],
    return_exceptions: bool = False,
    timeout: float | None = None,
) -> list[Any]:
    """
    Gather coroutines with fail-fast behavior and optional timeout.

    Unlike asyncio.gather:
    - Cancels remaining tasks on first exception (unless return_exceptions=True)
    - Supports timeout with automatic cleanup
    - Handles cancellation more gracefully
    """
    if timeout is not None:
        return await asyncio.wait_for(
            _safe_gather_impl(*coros, return_exceptions=return_exceptions),
            timeout=timeout,
        )
    return await _safe_gather_impl(*coros, return_exceptions=return_exceptions)


async def _safe_gather_impl(
    *coros: Coroutine[Any, Any, Any],
    return_exceptions: bool = False,
) -> list[Any]:
    tasks = [asyncio.create_task(coro) for coro in coros]

    if return_exceptions:
        return await asyncio.gather(*tasks, return_exceptions=True)

    try:
        return await asyncio.gather(*tasks)
    except Exception:
        # Cancel remaining tasks on failure
        for task in tasks:
            if not task.done():
                task.cancel()
        # Wait for cancellation to complete
        await asyncio.gather(*tasks, return_exceptions=True)
        raise
```

### Pattern: All tasks must succeed (fail-fast)

```python
# Initialization - all workers must start
await safe_gather(*[worker.pre_run() for worker in workers])

# Data fetching - need all pieces
channel_info, front_row, participants = await safe_gather(
    fetch_channel_info(channel_id),
    fetch_front_row(channel_id),
    fetch_participants(channel_id),
)

# Validation - all must pass
await safe_gather(*validation_tasks)

# With timeout
results = await safe_gather(*tasks, timeout=30.0)
```

### Pattern: Partial results acceptable

```python
# Use safe_gather with return_exceptions=True
# Benefits: timeout support + cleaner cancellation vs asyncio.gather
results = await safe_gather(*batch_tasks, return_exceptions=True)
for result in results:
    if isinstance(result, Exception):
        logger.error(f"Task failed: {result}")
    else:
        process(result)

# With timeout for partial results
results = await safe_gather(*batch_tasks, return_exceptions=True, timeout=30.0)
```

### Pattern: Cleanup/shutdown

```python
# Prefer safe_gather (better cancellation handling)
await safe_gather(*cancelled_tasks, return_exceptions=True)

# With timeout for cleanup
await safe_gather(
    service1.shutdown(),
    service2.shutdown(),
    service3.shutdown(),
    return_exceptions=True,
    timeout=10.0  # Don't wait forever for cleanup
)

# OK to keep existing asyncio.gather (migration is optional, both work)
await asyncio.gather(*cancelled_tasks, return_exceptions=True)
```

### When to Keep asyncio.gather

**Only keep for existing code to avoid churn:**
- Existing cleanup/shutdown code that works fine
- Low priority to migrate (both behaviors are equivalent)
- Focus migration efforts on new code and high-value patterns

## Migration Decision Tree

```
Is this new code you're writing?
├─ Yes -> Use safe_gather
└─ No (existing code)
   └─ Is it cleanup/shutdown with return_exceptions=True?
      ├─ Yes -> Keep asyncio.gather (optional to migrate)
      └─ No -> Evaluate migration benefit
         └─ Would fail-fast or timeout help?
            ├─ Yes -> Migrate to safe_gather
            └─ No -> Low priority, either is fine
```

## Common Patterns

### Initialization Pattern
```python
# Old
await asyncio.gather(*[worker.pre_run() for worker in workers])

# New
await safe_gather(*[worker.pre_run() for worker in workers])
```

### Tuple Unpacking Pattern
```python
# Old
first, last = await asyncio.gather(
    get_first_item(id),
    get_last_item(id),
)

# New
first, last = await safe_gather(
    get_first_item(id),
    get_last_item(id),
)
```

### Cleanup Pattern (DO NOT CHANGE unless adding timeout)
```python
# Correct - keep as-is
await asyncio.gather(*self._running_tasks, return_exceptions=True)

# Or upgrade if you want timeout
await safe_gather(*self._running_tasks, return_exceptions=True, timeout=10.0)
```

## Key Principles

1. **Fail-fast by default**: If one task fails, cancel the rest immediately
2. **Always consider timeout**: Long-running operations should have timeouts
3. **Clean cancellation**: Always handle CancelledError properly
4. **Partial results when appropriate**: Use `return_exceptions=True` for batch operations
5. **Don't wait forever**: Especially for cleanup/shutdown operations

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/gigaverse-app) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
