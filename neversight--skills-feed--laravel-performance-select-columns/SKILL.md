---
name: laravel-performance-select-columns
description: Select only required columns to reduce memory and transfer costs; apply to base queries and relations Use when this capability is needed.
metadata:
  author: neversight
---

# Select Only Needed Columns

Reduce payloads by selecting exact fields:

```php
User::select(['id', 'name'])->paginate();

Post::with(['author:id,name'])->select(['id','author_id','title'])->get();
```

- Avoid `*`; keep DTOs/resources aligned with selected fields
- Combine with eager loading to avoid N+1

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neversight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
