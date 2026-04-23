---
name: mags-docs-search
description: Search across all project documents Use when this capability is needed.
metadata:
  author: doancan
---

# MAGS Docs Search

Search across all project documents using full-text search.

## Usage

```
/mags-docs-search <query>
```

## Steps

1. Parse the query from the argument.
2. Call `mags_search_docs` with the query string.
3. Display results ranked by relevance:
   ```
   == Search: "<query>" ==

   1. docs/architecture/overview.md
      ...matching excerpt with context...

   2. docs/rules/backend.md
      ...matching excerpt with context...

   Found <N> results.
   ```
4. If no results, suggest: "No matches. Try broader terms or run `/mags-docs` to browse."

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/doancan) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
