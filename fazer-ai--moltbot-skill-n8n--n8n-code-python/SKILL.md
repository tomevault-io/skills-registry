---
name: n8n-code-python
description: Write Python code in n8n Code nodes. Use when writing Python for n8n, using Python libraries, pandas data processing, or preferring Python over JavaScript in workflows. Use when this capability is needed.
metadata:
  author: fazer-ai
---

# n8n Code Python

Expert guidance for writing Python in n8n Code nodes.

> Based on [n8n-skills](https://github.com/czlonkowski/n8n-skills) by Romuald Członkowski

## Python Code Node Basics

n8n supports Python in Code nodes with some differences from JavaScript.

### Run Once for All Items

```python
# Access all input items
items = _input.all()

# Process and return
return [{"json": {**item.json, "processed": True}} for item in items]
```

### Run Once for Each Item

```python
# Access current item
data = _input.item.json

# Return single item
return {"json": {**data, "processed": True}}
```

## Accessing Data

### Current Node Input

```python
# All items
items = _input.all()

# First item
first = _input.first()

# Current item (in "each item" mode)
current = _input.item

# JSON data
data = _input.first().json
```

### Execution Context

```python
# Note: Limited compared to JavaScript
# Access via items or hardcoded values
```

## Common Patterns

### Transform All Items

```python
items = _input.all()

return [
    {
        "json": {
            "id": item.json["id"],
            "name": item.json["name"].upper(),
            "processed": True
        }
    }
    for item in items
]
```

### Filter Items

```python
items = _input.all()

return [
    {"json": item.json}
    for item in items
    if item.json.get("status") == "active"
]
```

### Aggregate Data

```python
items = _input.all()

total = sum(item.json.get("amount", 0) for item in items)
count = len(items)

return [{
    "json": {
        "total": total,
        "count": count,
        "average": total / count if count > 0 else 0
    }
}]
```

### Group By

```python
from collections import defaultdict

items = _input.all()
grouped = defaultdict(list)

for item in items:
    key = item.json.get("category", "unknown")
    grouped[key].append(item.json)

return [
    {"json": {"category": k, "items": v, "count": len(v)}}
    for k, v in grouped.items()
]
```

### JSON Parsing

```python
import json

items = _input.all()

return [
    {
        "json": {
            "parsed": json.loads(item.json.get("rawData", "{}"))
        }
    }
    for item in items
]
```

### Error Handling

```python
import json

items = _input.all()
results = []

for item in items:
    try:
        parsed = json.loads(item.json.get("rawData", "{}"))
        results.append({"json": {"success": True, "data": parsed}})
    except Exception as e:
        results.append({"json": {"success": False, "error": str(e)}})

return results
```

## Date/Time Handling

```python
from datetime import datetime, timedelta

# Current time
now = datetime.now()

# Parse ISO date
date = datetime.fromisoformat("2024-01-15T10:30:00")

# Format
formatted = now.strftime("%Y-%m-%d %H:%M:%S")

# Add/subtract
next_week = now + timedelta(days=7)
yesterday = now - timedelta(days=1)

# Return with dates
return [{
    "json": {
        "now": now.isoformat(),
        "formatted": formatted,
        "nextWeek": next_week.isoformat()
    }
}]
```

## Working with Pandas

⚠️ **Note**: pandas may not be available in all n8n installations. Check your environment.

```python
import pandas as pd

items = _input.all()

# Convert to DataFrame
data = [item.json for item in items]
df = pd.DataFrame(data)

# Process with pandas
df["total"] = df["price"] * df["quantity"]
df_grouped = df.groupby("category").agg({"total": "sum"}).reset_index()

# Convert back to n8n format
return [{"json": row} for row in df_grouped.to_dict(orient="records")]
```

## Webhook Data Access

**CRITICAL**: Webhook data is under `.body`:

```python
# ❌ Wrong
message = _input.first().json.get("message")

# ✅ Correct - webhook data is in body
body = _input.first().json.get("body", {})
message = body.get("message")
headers = _input.first().json.get("headers", {})
query = _input.first().json.get("query", {})
```

## Available Libraries

Standard Python libraries typically available:

- `json` - JSON parsing
- `datetime` - Date/time handling
- `re` - Regular expressions
- `collections` - Data structures
- `math` - Mathematical functions
- `base64` - Encoding
- `hashlib` - Hashing

```python
import json
import re
import hashlib
from datetime import datetime
from collections import defaultdict

# Use hashlib
hash_value = hashlib.sha256(data.encode()).hexdigest()

# Use regex
matches = re.findall(r"\d+", text)
```

## Common Mistakes

### 1. Wrong Return Format

```python
# ❌ Wrong - must return list of dicts with "json" key
return {"name": "John"}

# ✅ Correct
return [{"json": {"name": "John"}}]
```

### 2. Using dot notation for dict access

```python
# ❌ Wrong - Python dicts use brackets
name = item.json.name

# ✅ Correct
name = item.json["name"]
# or safer:
name = item.json.get("name", "default")
```

### 3. Not Handling Missing Keys

```python
# ❌ Wrong - KeyError if missing
value = item.json["optional_field"]

# ✅ Correct
value = item.json.get("optional_field", "default")
```

### 4. Returning dict instead of list

```python
# ❌ Wrong in "all items" mode
return {"json": {"result": "data"}}

# ✅ Correct - always return list
return [{"json": {"result": "data"}}]
```

## Python vs JavaScript in n8n

| Feature | Python | JavaScript |
|---------|--------|------------|
| Input access | `_input` | `$input` |
| Node access | Limited | `$node["Name"]` |
| Env vars | Limited | `$env.VAR` |
| Async/await | No | Yes |
| Libraries | Standard + limited | Luxon, Lodash |

## Best Practices

1. **Always return list** of `{"json": {...}}` dicts
2. **Use `.get()`** for safe dict access
3. **Handle exceptions** with try/except
4. **Import at top** of code block
5. **Test with simple data** first
6. **Access webhook data** via `.body`

## Quick Reference

| Need | Code |
|------|------|
| All items | `_input.all()` |
| First item | `_input.first()` |
| Current item | `_input.item` (each mode) |
| Item JSON | `item.json` |
| Dict value | `item.json.get("key", default)` |
| Webhook body | `item.json.get("body", {})` |
| Return item | `{"json": {...}}` |
| Return many | `[{"json": {...}}, ...]` |

---

<sub>Based on [n8n-skills](https://github.com/czlonkowski/n8n-skills) by Romuald Członkowski • Adapted for Moltbot by fazer.ai</sub>

---
> Source: [fazer-ai/moltbot-skill-n8n](https://github.com/fazer-ai/moltbot-skill-n8n) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-10 -->
