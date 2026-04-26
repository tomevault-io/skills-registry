---
name: test-json-sql-pluck
description: Tests pluck primitive (SELECT single field)
metadata:
  author: bdambrosio
---

# Test Pluck Primitive

**Self-contained:** Creates test data internally

**Input:** Creates $papers collection (4 papers with id, title, year, citations, venue)

**Operation:** Pluck title field from each paper

**Expected Output:** $titles collection with 4 items, each containing just the title string

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/bdambrosio) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
