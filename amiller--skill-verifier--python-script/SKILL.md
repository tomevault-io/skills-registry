---
name: python-example
description: Python skill with dependencies Use when this capability is needed.
metadata:
  author: amiller
---

# Python Example Skill

Demonstrates a Python skill with pip dependencies and tests.

## Files

- `requirements.txt` - Python dependencies
- `skill.py` - Main skill code
- `test.py` - Tests

## Testing Locally

```bash
pip install -r requirements.txt
python test.py
```

## Verify

```bash
curl -X POST http://localhost:3000/verify \
  -H "Content-Type: application/json" \
  -d '{"skillPath": "./examples/python-script"}'
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/amiller) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
