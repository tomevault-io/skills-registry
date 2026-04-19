---
name: python
description: Python language conventions, patterns, and tooling. This skill should be used when working with Python source files (.py), pyproject.toml, or writing Python scripts. Use when this capability is needed.
metadata:
  author: scnewma
---

# Python

## Tooling

- Formatter/linter: `uvx ruff`
- Package manager: `uv`

## Inline Script Dependencies

For single-file scripts, use uv inline script dependencies instead of a separate requirements file:

```python
# /// script
# dependencies = ["requests", "rich"]
# ///
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/scnewma) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
