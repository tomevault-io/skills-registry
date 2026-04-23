---
name: python-pathlib
description: Python pathlib usage guidelines for file and directory operations Use when this capability is needed.
metadata:
  author: jr2804
---

# Python Pathlib

## What I Do

Provide guidelines for using Python's pathlib module for all file system paths.

## Path Rules

### Always Use Pathlib

```python
# GOOD - Use pathlib for all paths
from pathlib import Path

config_path = Path("config/settings.json")
data_dir = Path("data/output")
file_path = data_dir / "results.csv"

# BAD - Never store paths as strings
config_path = "config/settings.json"  # WRONG
data_dir = "data/output"              # WRONG

# BAD - Never use os.path
import os.path
path = os.path.join("data", "output")  # WRONG
```

### Path Operations

```python
from pathlib import Path

# Create paths with / operator
base = Path("data")
output_file = base / "results.csv"
subdir_file = base / "processed" / "output.json"

# Check path types
path.is_file()      # True if file
path.is_dir()       # True if directory
path.exists()       # True if exists

# File operations
path.read_text()        # Read as string
path.read_bytes()       # Read as bytes
path.write_text(content)  # Write string
path.write_bytes(data)    # Write bytes

# Directory operations
path.mkdir(parents=True, exist_ok=True)
path.iterdir()          # List directory contents
path.rglob("*.py")      # Recursive glob
```

### Context Managers

```python
from pathlib import Path

# Always use with statements for file handling
input_file = Path("input.txt")

with input_file.open("r") as f:
    data = f.read()

# Or use pathlib's read methods for simple cases
content = Path("config.json").read_text()
```

## When to Use Me

Use this skill when:

- Handling file paths
- Managing directory operations
- Reading or writing files
- Any Path-related work

## Key Rules

1. **Never use strings for paths** - Always use Path objects
2. **Never use os.path** - Use pathlib instead
3. **Use / operator** - For path concatenation
4. **Use with statements** - For file I/O
5. **Type annotate as Path** - `path: Path`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jr2804) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
