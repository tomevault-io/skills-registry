---
name: documentation
description: Update documentation when code changes Use when this capability is needed.
metadata:
  author: Computational-Rare-Disease-Genomics-WHG
---

# Documentation

When to update which docs.

## Rules

Documentation changes should land in same commit as the code.

| Change           | Update                       |
| ---------------- | ---------------------------- |
| New public API   | README.md, docs, APIDocs.tsx |
| New private API  | docs only                    |
| Schema change    | docs/schema.md + migration   |
| New feature      | README.md or docs/           |
| New env var      | .env.example + docs          |
| User-facing page | docs + possible screenshots  |

## APIDocs

Update `src/pages/APIDocs.tsx` only for endpoints that are:

1. Publicly accessible without auth
2. Intended for external consumers

Don't update for private/authenticated endpoints.

---
> Source: [Computational-Rare-Disease-Genomics-WHG/RNUdb](https://github.com/Computational-Rare-Disease-Genomics-WHG/RNUdb) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-22 -->
