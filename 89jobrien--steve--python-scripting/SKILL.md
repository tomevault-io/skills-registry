---
name: python-scripting
description: Python scripting with uv and PEP 723 inline dependencies. Use when creating Use when this capability is needed.
metadata:
  author: 89jobrien
---

# Python Scripting Skill

Creates self-contained Python scripts using uv and PEP 723 inline script metadata.

## What This Skill Does

- Creates standalone Python scripts
- Uses PEP 723 inline dependencies
- Sets up argument parsing
- Handles input/output
- Configures reproducible builds

## When to Use

- Standalone utility scripts
- One-off automation tasks
- Quick data processing
- CLI tools
- Scripts that need dependencies

## Reference Files

- `references/UV_SCRIPT.template.py` - Python script template with PEP 723 metadata

## PEP 723 Format

```python
#!/usr/bin/env -S uv run --script
# /// script
# requires-python = ">=3.12"
# dependencies = [
#   "requests",
#   "rich",
# ]
# ///
```

## Running Scripts

```bash
uv run script.py [args]
```

Dependencies install automatically on first run.

## Best Practices

- Use `exclude-newer` for reproducibility
- Include docstring with usage examples
- Use argparse for CLI arguments
- Return exit codes (0 success, non-zero error)
- Keep scripts focused on one task

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/89jobrien) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
