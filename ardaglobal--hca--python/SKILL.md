---
name: python
description: Run, test, and lint Python projects Use when this capability is needed.
metadata:
  author: ardaglobal
---

# Python Development Skill

You are working in a Python environment with modern tooling.

## Available Tools

- `python3` - Python interpreter
- `uv` - Fast package installer (preferred)
- `pip` - Standard package installer
- `poetry` - Dependency management

## Project Detection

Look for these files to identify Python projects:
- `pyproject.toml` - Modern project config
- `setup.py` - Legacy setup
- `requirements.txt` - Dependencies
- `poetry.lock` - Poetry lockfile
- `uv.lock` - UV lockfile

## Common Workflows

### Install Dependencies
```bash
# With uv (fastest)
uv pip install -r requirements.txt 2>&1

# With poetry
poetry install 2>&1

# With pip
pip install -r requirements.txt 2>&1
```

### Run Tests
```bash
pytest 2>&1
# or with verbose
pytest -v 2>&1
```

### Lint
```bash
ruff check . 2>&1
```

### Format
```bash
ruff format . 2>&1
# or
black . 2>&1
```

### Type Check
```bash
mypy . 2>&1
```

### Import Sorting
```bash
isort . --check 2>&1
```

## Poetry Projects

```bash
poetry install          # Install deps
poetry run pytest       # Run tests
poetry run python main.py  # Run script
```

## Virtual Environments

```bash
# Create venv
python3 -m venv .venv
source .venv/bin/activate

# Or with uv
uv venv
source .venv/bin/activate
```

## Best Practices

1. Use `ruff` for fast linting (replaces flake8, isort, etc.)
2. Use `uv` for fast package installation
3. Always run type checks with `mypy`
4. Check for `pyproject.toml` vs `requirements.txt`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ardaglobal) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
