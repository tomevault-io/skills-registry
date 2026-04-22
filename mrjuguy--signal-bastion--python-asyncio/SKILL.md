---
name: python-asyncio
description: Best practices for writing high-performance async Python code. Use when this capability is needed.
metadata:
  author: mrjuguy
---

# Python Asyncio Skill

## Core Philosophy

We use `asyncio` to handle I/O bound operations (Network, DB) without blocking the main thread. This is critical for catching arbitrage opportunities where latency is key.

## Best Practices

### 1. Defines

- **Always** use `async def` for I/O bound functions.
- **Never** perform blocking CPU operations (heavy math) inside an async loop without `run_in_executor`.
- **Naming**: Async functions should ideally be named logically (e.g., `fetch_ticker` not `async_fetch_ticker`).

### 2. Context Managers

- Use async context managers for resources.

```python
# GOOD
async with aiohttp.ClientSession() as session:
    async with session.get(url) as resp:
        data = await resp.json()

# BAD
session = aiohttp.ClientSession()
resp = await session.get(url) # Missing cleanup!
```

### 3. Error Handling

- Async tasks can fail silently if not awaited or gathered.
- Use `asyncio.gather(*tasks, return_exceptions=True)` to better handle bulk failures.

### 4. Event Loop

- Do not call `asyncio.run()` inside code that is already in a loop.
- Application Entry point should have ONE `asyncio.run(main())`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mrjuguy) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
