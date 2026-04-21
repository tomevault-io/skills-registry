---
name: go-concurrency-patterns
description: Patterns for concurrent execution, worker pools, and pipelines Use when this capability is needed.
metadata:
  author: mthang1801
---

## Concepts
- Worker pool
- errgroup (fan-out/fan-in)
- Pipeline
- Semaphore rate limiting
- Timeout & cancellation
- Query Builder specific: auto-refresh, CSV import progress

## Example

```go
// ✅ GOOD: errgroup for parallel execution
g, ctx := errgroup.WithContext(ctx)
g.SetLimit(10)

for _, card := range cards {
    card := card
    g.Go(func() error {
        return executeCard(ctx, card)
    })
}

g.Wait()
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mthang1801) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
