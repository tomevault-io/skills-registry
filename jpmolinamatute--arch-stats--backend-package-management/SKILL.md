---
name: backend-package-management
description: How to use uv to install/uninstall/update python packages Use when this capability is needed.
metadata:
  author: jpmolinamatute
---

# Python Package Management

We use uv to install, uninstall and update python packages. We also use
./backend/pyproject.toml to configure uv.

## Install dependencies

```bash
cd ./backend
uv add <package_name>
# or
uv add --dev <package_name>
```

## Uninstall dependencies

```bash
cd ./backend
uv remove <package_name>
# or
uv remove --dev <package_name>
```

## Update dependencies

```bash
cd ./backend
uv sync --upgrade
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jpmolinamatute) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
