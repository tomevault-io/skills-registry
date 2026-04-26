---
name: test-json-sql-semantic-scholar
description: Test JSON SQL primitives with semantic-scholar output Use when this capability is needed.
metadata:
  author: bdambrosio
---

# test-json-sql-semantic-scholar

Tests JSON SQL primitives (project, pluck, filter-structured, sort) with real semantic-scholar output.

## What it tests
- semantic-scholar returns Collection of paper Notes
- project extracts metadata.title, metadata.year, metadata.citations
- pluck extracts first title
- filter-structured filters by metadata.citations > 0
- sort orders by metadata.citations descending

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/bdambrosio) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
