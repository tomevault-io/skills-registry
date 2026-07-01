---
name: scienceskillscommon
description: >- Use when this capability is needed.
metadata:
  author: mkurman
---

# Science Skills Common

This is a shared Python package, not an agent skill. Skills import it as:

```python
from science_skills.scienceskillscommon import http_client
```

Each skill declares this as a dependency in its inline `uv` script header, so it
is installed automatically on first use.

This SKILL.md file is included so that standard skill installers automatically
discover and install this package alongside the skills that depend on it.

---
> Source: [mkurman/zorai](https://github.com/mkurman/zorai) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-07-01 -->
