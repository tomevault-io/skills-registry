---
name: query-builder-platform
description: Specific patterns for the Query Builder Platform (multi-DB, dashboards) Use when this capability is needed.
metadata:
  author: mthang1801
---

## Features
- Multi-database query engine (Strategy pattern)
- Parallel dashboard execution (errgroup)
- Streaming CSV import (memory-efficient)
- WebSocket real-time updates
- Query result caching
- Security (SQL injection, RLS)

## Example

```go
// ✅ Parallel dashboard execution (3.5x faster)
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
