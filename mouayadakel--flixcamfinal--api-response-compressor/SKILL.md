---
name: api-response-compressor
description: Reduces API payload size via select/pagination, compression, and caching headers. Use when responses are large (>100KB) or high-traffic. Use when this capability is needed.
metadata:
  author: mouayadakel
---

# API Response Compressor

## When to Trigger

- Large API responses (>100 KB)
- High-traffic endpoints
- "Optimize API"

## What to Do

1. **Select**: Return only needed fields (Prisma select); avoid deep include of unused relations.
2. **Pagination**: Enforce limit and offset/cursor; return meta (total, page, hasMore).
3. **Compression**: Enable gzip/brotli in Next.js or hosting; set Content-Encoding when applicable.
4. **Headers**: Cache-Control for cacheable responses (e.g. s-maxage, stale-while-revalidate).

Don’t break existing clients; add fields optionally (e.g. ?fields=) if needed. Measure response size before/after.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mouayadakel) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
