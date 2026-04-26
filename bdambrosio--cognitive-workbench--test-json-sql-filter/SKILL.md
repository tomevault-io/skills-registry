---
name: test-json-sql-filter
description: Tests filter-structured primitive (WHERE clause)
metadata:
  author: bdambrosio
---

# Test Filter-Structured Primitive

**Self-contained:** Creates test data internally

**Input:** Creates $papers collection (citations: 100, 250, 50, 180)

**Operation:** Filter where citations > 100

**Expected Output:** $high_cited collection with 2 items (Transformers: 250, Scaling Laws: 180)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/bdambrosio) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
