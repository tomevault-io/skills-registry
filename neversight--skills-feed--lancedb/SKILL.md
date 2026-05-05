---
name: lancedb
description: > Use when this capability is needed.
metadata:
  author: neversight
---

## Critical Patterns

### Table Creation (REQUIRED)

```python
import lancedb

# ✅ ALWAYS: Define schema clearly
db = lancedb.connect("./my_db")

data = [
    {"id": 1, "text": "Hello world", "vector": [0.1, 0.2, ...]},
    {"id": 2, "text": "Goodbye world", "vector": [0.3, 0.4, ...]},
]

table = db.create_table("my_table", data)
```

### Vector Search (REQUIRED)

```python
# ✅ Search by vector similarity
results = table.search([0.1, 0.2, ...]).limit(10).to_list()

# ✅ With filter
results = table.search(query_vector) \
    .where("category = 'tech'") \
    .limit(5) \
    .to_list()
```

---

## Decision Tree

```
Need semantic search?      → Use vector search
Need exact match?          → Use where clause
Need hybrid search?        → Combine vector + filter
Need persistence?          → Use file-based connection
```

---

## Resources

- **Best Practices**: [best-practices.md](best-practices.md)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neversight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
