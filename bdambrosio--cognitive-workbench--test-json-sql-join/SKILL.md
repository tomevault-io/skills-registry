---
name: test-json-sql-join
description: Tests join primitive (INNER JOIN)
metadata:
  author: bdambrosio
---

# Test Join Primitive

**Self-contained:** Creates test data internally

**Input:** 
- Creates $papers: A, B, C, D
- Creates $authors: A (Alice), B (Bob), E (Eve)

**Operation:** Inner join on id field

**Expected Output:** $joined collection with 2 items (A and B have matches)
- Paper A + Author Alice
- Paper B + Author Bob

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/bdambrosio) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
