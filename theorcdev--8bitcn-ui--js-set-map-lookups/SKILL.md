---
name: js-set-map-lookups
description: Use Set and Map for O(1) membership lookups instead of array.includes(). Apply when checking membership repeatedly or performing frequent lookups against a collection. Use when this capability is needed.
metadata:
  author: theorcdev
---

## Use Set/Map for O(1) Lookups

Convert arrays to Set/Map for repeated membership checks.

**Incorrect (O(n) per check):**

```typescript
const allowedIds = ['a', 'b', 'c', ...]
items.filter(item => allowedIds.includes(item.id))
```

**Correct (O(1) per check):**

```typescript
const allowedIds = new Set(['a', 'b', 'c', ...])
items.filter(item => allowedIds.has(item.id))
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/theorcdev) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
