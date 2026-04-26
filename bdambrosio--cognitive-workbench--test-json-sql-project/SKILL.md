---
name: test-json-sql-project
description: Tests project primitive (SELECT fields)
metadata:
  author: bdambrosio
---

# Test Project Primitive

**Self-contained:** Creates test data internally

**Input:** Creates $papers collection (4 papers with id, title, year, citations, venue)

**Operation:** Project only title and year fields

**Expected Output:** $projected collection with 4 items, each containing only {title, year}

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/bdambrosio) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
