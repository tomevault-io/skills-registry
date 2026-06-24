---
name: backend-virtual-environment
description: Activate the python virtual environment in the backend directory. Use when this capability is needed.
metadata:
  author: jpmolinamatute
---

# Activate Python Virtual Environment

Most task can be done via uv, but in certain cases you may want to run python commands
directly, in that case you need to activate the python virtual environment first.

```bash
cd ./backend
source .venv/bin/activate
```

if there is no virtual environment, create one:

```bash
cd ./backend
uv sync
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jpmolinamatute) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
