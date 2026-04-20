---
name: test
description: Run all project tests including Python pytest and JavaScript tests. Reports test results and failures. Use when this capability is needed.
metadata:
  author: iwatake2222
---

```bash
# Python
cd server && python -m pytest tests/ -v --tb=short

# JavaScript
cd client && npm test
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/iwatake2222) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
