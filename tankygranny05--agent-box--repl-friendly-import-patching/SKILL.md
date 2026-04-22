---
name: repl-friendly-import-patching
description: Adds project root directories to sys.path for proper imports, with try/except to handle REPL environments (PyCharm, ipython, Jupyter) where __file__ is undefined. Use when writing Python scripts that need to import from parent/grandparent directories and should also work in interactive environments. Use when this capability is needed.
metadata:
  author: tankygranny05
---

# REPL-Safe Import Fix

## Overview

This skill provides a reusable pattern to add parent/grandparent directories to `sys.path` for proper imports, while gracefully handling REPL environments where `__file__` is not defined.

## When To Use

- Writing Python scripts in subdirectories that need to import from parent folders
- Creating scripts that should work both as standalone files AND in REPL/interactive environments
- Setting up import paths for scripts in `src/scripts/`, `tests/`, or similar nested directories

## The Pattern

```python
import sys
from pathlib import Path

try:
    ROOT = Path(__file__).resolve().parents[2]  # Adjust index: 1=parent, 2=grandparent
    SRC_DIR = ROOT / "src"
    LIB_DIR = ROOT / "lib"
    for path in (SRC_DIR, LIB_DIR, ROOT):
        path_str = str(path)
        if path_str not in sys.path:
            sys.path.insert(0, path_str)
except Exception:
    """REPL environments (PyCharm, ipython, Jupyter) don't set __file__."""
    pass

# Now imports work from project root
from common import Utils  # Works!
```

## Customization

**Adjust `.parents[N]` based on script location:**
- `parents[0]` = same directory as script
- `parents[1]` = parent directory
- `parents[2]` = grandparent (e.g., `src/scripts/foo.py` → project root)

**Add more directories as needed:**
```python
for path in (SRC_DIR, LIB_DIR, TESTS_DIR, ROOT):
```

## Notes

- Uses `sys.path.insert(0, ...)` to prioritize project imports over system packages
- Checks `if path_str not in sys.path` to avoid duplicates
- The `except Exception` block silently passes in REPL—assumes REPL user has already set up paths or is in the correct directory

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tankygranny05) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
