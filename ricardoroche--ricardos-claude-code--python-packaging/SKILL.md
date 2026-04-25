---
name: python-packaging
description: Automatically applies when configuring Python project packaging. Ensures proper pyproject.toml setup, project layout, build configuration, metadata, and distribution best practices. Use when this capability is needed.
metadata:
  author: ricardoroche
---

# Python Packaging Patterns

When packaging Python projects, follow these patterns for modern, maintainable package configuration.

**Trigger Keywords**: pyproject.toml, packaging, setup.py, build, distribution, package, publish, PyPI, wheel, sdist, project structure

**Agent Integration**: Used by `backend-architect`, `devops-engineer`, `python-engineer`

## ✅ Correct Pattern: pyproject.toml Setup

```toml
# pyproject.toml - Modern Python packaging
[build-system]
requires = ["setuptools>=68.0", "wheel"]
build-backend = "setuptools.build_meta"

[project]
name = "mypackage"
version = "0.1.0"
description = "A short description of your package"
readme = "README.md"
license = {text = "MIT"}
authors = [
    {name = "Your Name", email = "your.email@example.com"}
]
maintainers = [
    {name = "Maintainer Name", email = "maintainer@example.com"}
]
classifiers = [
    "Development Status :: 4 - Beta",
    "Intended Audience :: Developers",
    "License :: OSI Approved :: MIT License",
    "Programming Language :: Python :: 3",
    "Programming Language :: Python :: 3.11",
    "Programming Language :: Python :: 3.12",
    "Topic :: Software Development :: Libraries",
]
keywords = ["example", "package", "keywords"]
requires-python = ">=3.11"

# Core dependencies
dependencies = [
    "fastapi>=0.109.0",
    "pydantic>=2.5.0",
    "sqlalchemy>=2.0.0",
    "httpx>=0.26.0",
]

# Optional dependencies (extras)
[project.optional-dependencies]
dev = [
    "pytest>=7.4.0",
    "pytest-cov>=4.1.0",
    "pytest-asyncio>=0.23.0",
    "black>=24.0.0",
    "ruff>=0.1.0",
    "mypy>=1.8.0",
]
docs = [
    "sphinx>=7.2.0",
    "sphinx-rtd-theme>=2.0.0",
]
all = [
    "mypackage[dev,docs]",
]

[project.urls]
Homepage = "https://github.com/username/mypackage"
Documentation = "https://mypackage.readthedocs.io"
Repository = "https://github.com/username/mypackage.git"
Issues = "https://github.com/username/mypackage/issues"
Changelog = "https://github.com/username/mypackage/blob/main/CHANGELOG.md"

[project.scripts]
# Entry points for CLI commands
mypackage-cli = "mypackage.cli:main"

# Tool configurations
[tool.setuptools]
# Package discovery
packages = ["mypackage", "mypackage.submodule"]

[tool.setuptools.package-data]
mypackage = ["py.typed", "*.json", "templates/*.html"]

[tool.pytest.ini_options]
testpaths = ["tests"]
python_files = ["test_*.py"]
python_classes = ["Test*"]
python_functions = ["test_*"]
addopts = [
    "--strict-markers",
    "--cov=mypackage",
    "--cov-report=term-missing",
    "--cov-report=html",
]
markers = [
    "slow: marks tests as slow",
    "integration: marks tests as integration tests",
]

[tool.black]
line-length = 100
target-version = ["py311", "py312"]
include = '\.pyi?$'
exclude = '''
/(
    \.git
  | \.venv
  | build
  | dist
)/
'''

[tool.ruff]
line-length = 100
target-version = "py311"
select = [
    "E",  # pycodestyle errors
    "W",  # pycodestyle warnings
    "F",  # pyflakes
    "I",  # isort
    "B",  # flake8-bugbear
    "C4", # flake8-comprehensions
    "UP", # pyupgrade
]
ignore = []
exclude = [
    ".git",
    ".venv",
    "build",
    "dist",
]

[tool.mypy]
python_version = "3.11"
strict = true
warn_return_any = true
warn_unused_configs = true
disallow_untyped_defs = true
disallow_any_generics = true
no_implicit_optional = true
warn_redundant_casts = true
warn_unused_ignores = true
warn_no_return = true
check_untyped_defs = true

[[tool.mypy.overrides]]
module = "tests.*"
disallow_untyped_defs = false
```

## Project Structure

```
mypackage/
├── pyproject.toml          # Project configuration
├── README.md               # Project documentation
├── LICENSE                 # License file
├── CHANGELOG.md            # Version history
├── .gitignore              # Git ignore patterns
│
├── src/                    # Source code (src layout)
│   └── mypackage/
│       ├── __init__.py     # Package init
│       ├── __main__.py     # Entry point for -m
│       ├── py.typed        # PEP 561 marker
│       ├── core.py
│       ├── models.py
│       └── utils/
│           ├── __init__.py
│           └── helpers.py
│
├── tests/                  # Test suite
│   ├── __init__.py
│   ├── conftest.py        # Pytest fixtures
│   ├── test_core.py
│   └── test_models.py
│
├── docs/                   # Documentation
│   ├── conf.py
│   ├── index.rst
│   └── api.rst
│
└── scripts/                # Utility scripts
    └── setup_dev.sh
```

## Package Init

```python
# src/mypackage/__init__.py
"""
MyPackage - A Python package for doing things.

This package provides tools for X, Y, and Z.
"""

from mypackage.core import MainClass, main_function
from mypackage.models import Model1, Model2

# Version
__version__ = "0.1.0"

# Public API
__all__ = [
    "MainClass",
    "main_function",
    "Model1",
    "Model2",
]


# Convenience imports for common use cases
def quick_start():
    """Quick start helper for new users."""
    return MainClass()
```

## CLI Entry Point

```python
# src/mypackage/__main__.py
"""
Entry point for python -m mypackage.
"""
from mypackage.cli import main

if __name__ == "__main__":
    main()


# src/mypackage/cli.py
"""Command-line interface."""
import argparse
import sys
from typing import List, Optional


def main(argv: Optional[List[str]] = None) -> int:
    """
    Main CLI entry point.

    Args:
        argv: Command-line arguments (defaults to sys.argv)

    Returns:
        Exit code (0 for success, non-zero for error)
    """
    parser = argparse.ArgumentParser(
        prog="mypackage",
        description="MyPackage CLI tool",
    )

    parser.add_argument(
        "--version",
        action="version",
        version=f"%(prog)s {__version__}"
    )

    parser.add_argument(
        "--verbose",
        "-v",
        action="store_true",
        help="Enable verbose output"
    )

    subparsers = parser.add_subparsers(dest="command", help="Available commands")

    # Add subcommands
    init_parser = subparsers.add_parser("init", help="Initialize project")
    init_parser.add_argument("path", help="Project path")

    run_parser = subparsers.add_parser("run", help="Run the application")
    run_parser.add_argument("--config", help="Config file path")

    # Parse arguments
    args = parser.parse_args(argv)

    # Execute command
    if args.command == "init":
        return init_command(args)
    elif args.command == "run":
        return run_command(args)
    else:
        parser.print_help()
        return 1


def init_command(args) -> int:
    """Handle init command."""
    print(f"Initializing project at {args.path}")
    return 0


def run_command(args) -> int:
    """Handle run command."""
    print("Running application")
    return 0


if __name__ == "__main__":
    sys.exit(main())
```

## Type Hints for Distribution

```python
# src/mypackage/py.typed
# This file signals that the package supports type hints (PEP 561)
# Leave empty or add configuration if needed
```

## Version Management

```python
# src/mypackage/_version.py
"""Version information."""

__version__ = "0.1.0"
__version_info__ = tuple(int(i) for i in __version__.split("."))


# src/mypackage/__init__.py
from mypackage._version import __version__, __version_info__

__all__ = ["__version__", "__version_info__"]
```

## Build and Distribution

```bash
# Build package
python -m build

# This creates:
# dist/mypackage-0.1.0-py3-none-any.whl  (wheel)
# dist/mypackage-0.1.0.tar.gz            (source distribution)

# Test installation locally
pip install dist/mypackage-0.1.0-py3-none-any.whl

# Upload to PyPI
python -m twine upload dist/*

# Upload to Test PyPI first
python -m twine upload --repository testpypi dist/*
```

## Manifest for Non-Python Files

```
# MANIFEST.in - Include additional files in source distribution
include README.md
include LICENSE
include CHANGELOG.md
include pyproject.toml

recursive-include src/mypackage *.json
recursive-include src/mypackage *.yaml
recursive-include src/mypackage/templates *.html
recursive-include tests *.py

global-exclude __pycache__
global-exclude *.py[co]
```

## Development Installation

```bash
# Install in editable mode with dev dependencies
pip install -e ".[dev]"

# Or with all optional dependencies
pip install -e ".[all]"

# This allows you to edit code without reinstalling
```

## GitHub Actions for Publishing

```yaml
# .github/workflows/publish.yml
name: Publish to PyPI

on:
  release:
    types: [published]

jobs:
  build-and-publish:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v4

      - name: Set up Python
        uses: actions/setup-python@v4
        with:
          python-version: "3.11"

      - name: Install dependencies
        run: |
          pip install build twine

      - name: Build package
        run: python -m build

      - name: Check distribution
        run: twine check dist/*

      - name: Publish to PyPI
        env:
          TWINE_USERNAME: __token__
          TWINE_PASSWORD: ${{ secrets.PYPI_TOKEN }}
        run: twine upload dist/*
```

## ❌ Anti-Patterns

```python
# ❌ Using setup.py instead of pyproject.toml
# setup.py is legacy, use pyproject.toml

# ❌ No version pinning in dependencies
dependencies = ["fastapi"]  # Which version?

# ✅ Better: Pin compatible versions
dependencies = ["fastapi>=0.109.0,<1.0.0"]


# ❌ Flat package structure
mypackage.py  # Single file, hard to scale

# ✅ Better: Proper package structure
src/mypackage/__init__.py


# ❌ No py.typed marker
# Users can't benefit from type hints

# ✅ Better: Include py.typed
src/mypackage/py.typed


# ❌ Not specifying python_requires
# Package might be installed on incompatible Python

# ✅ Better: Specify Python version
requires-python = ">=3.11"


# ❌ No optional dependencies
dependencies = ["pytest", "sphinx"]  # Forces install!

# ✅ Better: Use optional-dependencies
[project.optional-dependencies]
dev = ["pytest"]
docs = ["sphinx"]
```

## Best Practices Checklist

- ✅ Use pyproject.toml for configuration
- ✅ Use src/ layout for packages
- ✅ Include py.typed for type hints
- ✅ Specify Python version requirement
- ✅ Pin dependency versions
- ✅ Use optional-dependencies for extras
- ✅ Include README, LICENSE, CHANGELOG
- ✅ Define entry points for CLI tools
- ✅ Configure tools in pyproject.toml
- ✅ Test package installation locally
- ✅ Use build and twine for publishing
- ✅ Automate publishing with CI/CD

## Auto-Apply

When creating Python packages:
1. Use pyproject.toml for all configuration
2. Use src/package_name/ layout
3. Include py.typed marker
4. Define optional-dependencies for dev/docs
5. Pin dependency versions
6. Include README and LICENSE
7. Define CLI entry points if needed
8. Configure testing and linting tools

## Related Skills

- `dependency-management` - For managing dependencies
- `type-safety` - For type hints
- `pytest-patterns` - For testing
- `git-workflow-standards` - For releases
- `docs-style` - For documentation

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ricardoroche) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
