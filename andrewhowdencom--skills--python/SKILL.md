---
name: python
description: Guidelines for Python development, including environment setup and dependency management. Use when this capability is needed.
metadata:
  author: andrewhowdencom
---

# Python

## Virtual Environment
Ensure you are working within a virtual environment.

Check if `.venv/bin/activate` exists. If not, initialize it:

```bash
python3 -m venv .venv
```

Activate the environment:

```bash
source .venv/bin/activate
```

## Dependencies
If the project uses `pip` for dependency management (indicated by `setup.py` or `pyproject.toml`), install the package in editable mode to test changes:

```bash
pip install -e .
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/andrewhowdencom) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
