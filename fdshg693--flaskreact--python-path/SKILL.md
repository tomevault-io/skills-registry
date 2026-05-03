---
name: python-path
description: how to use global instance **PROJECTPATHS** in this project Use when this capability is needed.
metadata:
  author: fdshg693
---

# Skill Instructions

This project has a global singleton instance named `PROJECTPATHS` that manages all file paths consistently across the codebase.
It is defined in `src/config/paths.py` and auto-configured from the INI file `src/config/pahts.ini` at import time.

## Import

```python
# No initialization needed — importing triggers singleton creation automatically
from config import PROJECTPATHS

your_path = PROJECTPATHS.some_path_attribute  # returns a pathlib.Path object
```

Or import the module directly:

```python
from config.paths import PROJECTPATHS
```

## Path Attributes

All attributes return absolute `pathlib.Path` objects rooted at the project root.

Path attributes are defined in the INI file `src/config/pahts.ini` under the `[paths]` section as project-root-relative paths.
The visual tree of all paths is available in `src/config/paths.txt`.

## Utility Functions

In addition to `PROJECTPATHS`, `src/config/paths.py` also exports three utility functions.

### `get_path(*parts, root=None, create=False) -> Path`

Joins path parts from a base root. Optionally creates the directory.

```python
from config.paths import get_path, PROJECTPATHS

# Build a new output path under data/
p = get_path("data", "new_dir", create=True)

# Build relative to a custom root
p = get_path("results", root=PROJECTPATHS.outputs)
```

> Do not include filenames with extensions in `parts`; use `ensure_path_exists` for that.

### `find_paths(pattern, root=None, recursive=True) -> list[Path]`

Searches for files/directories matching a glob pattern under a root.

```python
from config.paths import find_paths, PROJECTPATHS

py_files = find_paths("*.py", recursive=False)
all_csv  = find_paths("**/*.csv", root=PROJECTPATHS.ml_data)
```

### `ensure_path_exists(path, *, is_file=False) -> Path`

Ensures a directory (or a file's parent directory) exists, creating it if needed.

```python
from config.paths import ensure_path_exists, PROJECTPATHS

# Ensure parent dir exists before writing a file
log_file = ensure_path_exists(PROJECTPATHS.logs / "app.log", is_file=True)

# Ensure a directory exists
cache_dir = ensure_path_exists(PROJECTPATHS.data / "cache")
```

## Configuration Source

Paths are loaded from `src/config/pahts.ini` at import time. The root is resolved in this order:

1. `PROJECT_ROOT` environment variable (if set)
2. Two levels up from `paths.py` (i.e., the repo root)

## Regenerating `paths.txt`

If `src/config/paths.txt` seems outdated (e.g., after editing the INI file), regenerate it with:

```bash
uv run tests/config/test_paths.py
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/fdshg693) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
