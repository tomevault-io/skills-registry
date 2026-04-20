---
name: lint
description: Run static analysis tools (pylint, flake8 for Python, ESLint for JavaScript) to check Google Style compliance. Use when this capability is needed.
metadata:
  author: iwatake2222
---

```bash
# Python
cd server && python -m flake8 src/ tests/ --max-line-length=80
cd server && python -m pylint src/ --indent-string='  '

# JavaScript
cd client && npx eslint src/ --ext .js
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/iwatake2222) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
