---
name: python-scripting
description: Guide for writing and debugging Python scripts. Use this when asked to create or fix Python code, automate tasks, or manipulate data using Python. Use when this capability is needed.
metadata:
  author: renan-alm
---
# Python Scripting Skill and Rules

Always create a requirements.txt for dependencies - List all external libraries your script uses in a requirements.txt file for easy installation via pip.

Always create a README.md to display the arguments for the script.

## Argument Parsing Choice

- Use `argparse` for command-line argument parsing
- Group required arguments first, then optional ones
- Use `type=str` explicitly for string arguments
- Use `action="store_true"` for boolean flags
- Provide clear `help` text for each argument
- Use lowercase with dashes for argument names (`--my-arg`)

Follow this structure:

```python
import argparse

def main():
    parser = argparse.ArgumentParser(
        description="Brief description of what the script does"
    )
    
    # Required arguments
    parser.add_argument("--token", required=True, type=str, help="Description of token")
    parser.add_argument("--name", required=True, type=str, help="Description of name")
    
    # Optional arguments with defaults
    parser.add_argument(
        "--optional-param",
        required=False,
        type=str,
        default="default_value",
        help="Description (comma-separated for lists)",
    )
    
    # Boolean flags
    parser.add_argument(
        "--enable-feature",
        action="store_true",
        default=False,
        required=False,
        help="Boolean to enable feature",
    )
    
    args = parser.parse_args()
    
    # Access arguments via args.token, args.name, etc.
    # Note: dashes become underscores (--optional-param -> args.optional_param)
```

## Authentication Strategy

When dealing with authentication tokens or API keys, follow this order of precedence for retrieving them:

The priority will always be an environment variable.

If an environment variable not found will fallback to trying to find a `.env` file which has the same variable name described.

If neither is found, you may use a `--token` argument to pass the value directly when executing the script.

## Function Descriptions

Endorse validation for function arguments and return types using type hints.

## Use a virtual environment

```bash
  rm -rf venv
  $PYTHON_BIN -m venv venv
  source venv/bin/activate
  python -m pip install --upgrade pip
  if [ -f requirements.txt ]; then
    pip install -r requirements.txt
  else
    echo 'INFO: No requirement files found!'
  fi
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/renan-alm) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
