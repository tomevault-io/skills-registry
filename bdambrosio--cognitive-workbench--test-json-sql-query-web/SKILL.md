---
name: test-json-sql-search-web
description: Test JSON SQL primitives with search-web output Use when this capability is needed.
metadata:
  author: bdambrosio
---

# test-json-sql-search-web

Tests JSON SQL primitives (project, pluck, filter-structured, sort) with real search-web output.

## What it tests
- search-web returns Collection of Notes
- project extracts metadata.uri, metadata.domain, char_count
- pluck extracts first URI
- filter-structured filters by char_count > 100
- sort orders by char_count descending

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/bdambrosio) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
