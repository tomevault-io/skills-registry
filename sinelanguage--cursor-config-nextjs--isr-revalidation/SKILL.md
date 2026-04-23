---
name: next-isr-revalidation
description: Add ISR and on-demand revalidation to Next.js routes. Use when this capability is needed.
metadata:
  author: sinelanguage
---
# Next.js ISR and Revalidation

Add incremental static regeneration with explicit revalidation and cache tags.

## When to Use

- Pages that can tolerate stale content
- Performance-sensitive routes

## Inputs

- Route segment
- Revalidation interval or tags
- Cache invalidation triggers

## Instructions

1. Use `revalidate` options on server fetches.
2. Add `revalidatePath` or `revalidateTag` where needed.
3. Ensure cache strategy is explicit and documented.
4. Document stale content tolerance per route.

## Output

- ISR-enabled route with revalidation and cache-tag strategy.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sinelanguage) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
