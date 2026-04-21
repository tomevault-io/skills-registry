---
name: python-venv
description: Bootstrap Python projects with virtual environments and modern tooling. Use when creating Python applications, scripts, APIs, data science projects, or when the user wants a properly configured Python development environment. Use when this capability is needed.
metadata:
  author: kadajett
---

# Python Virtual Environment Bootstrapper

Creates production-ready Python projects with virtual environments, dependency management, and modern tooling.

## Prerequisites

```bash
# Check Python (3.10+ recommended)
python3 --version

# Or on some systems
python --version
```

## Project Initialization Methods

### Method 1: Using uv (Recommended - Fastest)

[uv](https://github.com/astral-sh/uv) is an extremely fast Python package installer and resolver.

```bash
# Install uv
curl -LsSf https://astral.sh/uv/install.sh | sh

# Create project with virtual environment
uv init PROJECT_NAME
cd PROJECT_NAME
uv venv
source .venv/bin/activate  # Linux/macOS
# .venv\Scripts\activate   # Windows
```

### Method 2: Using Poetry

```bash
# Install Poetry
curl -sSL https://install.python-poetry.org | python3 -

# Create new project
poetry new PROJECT_NAME
cd PROJECT_NAME

# Or initialize in existing directory
mkdir PROJECT_NAME && cd PROJECT_NAME
poetry init
```

### Method 3: Using Standard venv

```bash
# Create project directory
mkdir PROJECT_NAME && cd PROJECT_NAME

# Create virtual environment
python3 -m venv .venv

# Activate
source .venv/bin/activate  # Linux/macOS
# .venv\Scripts\activate   # Windows

# Upgrade pip
pip install --upgrade pip
```

## Project Structure

```
my-python-project/
├── src/
│   └── my_project/
│       ├── __init__.py
│       └── main.py
├── tests/
│   ├── __init__.py
│   └── test_main.py
├── .venv/
├── pyproject.toml
├── README.md
├── .gitignore
└── .python-version
```

## pyproject.toml Configuration

Modern Python projects use `pyproject.toml` for all configuration:

```toml
[project]
name = "my-project"
version = "0.1.0"
description = "A brief description"
readme = "README.md"
requires-python = ">=3.10"
license = { text = "MIT" }
authors = [
    { name = "Your Name", email = "email@example.com" }
]
keywords = ["keyword1", "keyword2"]
classifiers = [
    "Development Status :: 3 - Alpha",
    "Intended Audience :: Developers",
    "License :: OSI Approved :: MIT License",
    "Programming Language :: Python :: 3",
    "Programming Language :: Python :: 3.10",
    "Programming Language :: Python :: 3.11",
    "Programming Language :: Python :: 3.12",
]
dependencies = []

[project.optional-dependencies]
dev = [
    "pytest>=8.0",
    "pytest-cov>=4.0",
    "ruff>=0.4",
    "mypy>=1.10",
    "pre-commit>=3.0",
]

[project.scripts]
my-project = "my_project.main:main"

[build-system]
requires = ["hatchling"]
build-backend = "hatchling.build"

[tool.hatch.build.targets.wheel]
packages = ["src/my_project"]

[tool.ruff]
target-version = "py310"
line-length = 100

[tool.ruff.lint]
select = [
    "E",    # pycodestyle errors
    "W",    # pycodestyle warnings
    "F",    # Pyflakes
    "I",    # isort
    "B",    # flake8-bugbear
    "C4",   # flake8-comprehensions
    "UP",   # pyupgrade
    "ARG",  # flake8-unused-arguments
    "SIM",  # flake8-simplify
]

[tool.mypy]
python_version = "3.10"
strict = true
warn_return_any = true
warn_unused_ignores = true

[tool.pytest.ini_options]
testpaths = ["tests"]
python_files = "test_*.py"
python_functions = "test_*"
addopts = "-v --cov=src/my_project --cov-report=term-missing"
```

## Essential Python .gitignore

```gitignore
# Virtual environments
.venv/
venv/
ENV/

# Python
__pycache__/
*.py[cod]
*$py.class
*.so
.Python
build/
dist/
*.egg-info/
.eggs/

# Testing
.pytest_cache/
.coverage
htmlcov/
.tox/

# Type checking
.mypy_cache/

# IDEs
.idea/
.vscode/
*.swp
*.swo

# Environment
.env
.env.local
*.env

# OS
.DS_Store
Thumbs.db
```

## Development Tools Setup

### Install development dependencies

```bash
# With uv
uv pip install -e ".[dev]"

# With pip
pip install -e ".[dev]"

# With Poetry
poetry install --with dev
```

### Configure pre-commit hooks

Create `.pre-commit-config.yaml`:

```yaml
repos:
  - repo: https://github.com/astral-sh/ruff-pre-commit
    rev: v0.4.4
    hooks:
      - id: ruff
        args: [--fix]
      - id: ruff-format

  - repo: https://github.com/pre-commit/mirrors-mypy
    rev: v1.10.0
    hooks:
      - id: mypy
        additional_dependencies: []

  - repo: https://github.com/pre-commit/pre-commit-hooks
    rev: v4.6.0
    hooks:
      - id: trailing-whitespace
      - id: end-of-file-fixer
      - id: check-yaml
      - id: check-added-large-files
```

Install hooks:

```bash
pre-commit install
```

## Project Templates by Type

### CLI Application

```bash
# Additional dependencies
pip install typer[all] rich
```

```python
# src/my_project/main.py
import typer
from rich import print

app = typer.Typer()

@app.command()
def hello(name: str = "World"):
    """Say hello to someone."""
    print(f"[green]Hello, {name}![/green]")

def main():
    app()

if __name__ == "__main__":
    main()
```

### Web API (FastAPI)

```bash
pip install fastapi uvicorn[standard]
```

```python
# src/my_project/main.py
from fastapi import FastAPI

app = FastAPI()

@app.get("/")
async def root():
    return {"message": "Hello World"}

@app.get("/items/{item_id}")
async def read_item(item_id: int, q: str | None = None):
    return {"item_id": item_id, "q": q}
```

### Data Science

```bash
pip install pandas numpy matplotlib jupyter
```

## Development Commands

```bash
# Run the application
python -m my_project.main

# Run tests
pytest

# Run tests with coverage
pytest --cov=src/my_project

# Lint
ruff check .

# Format
ruff format .

# Type check
mypy src/

# Run pre-commit on all files
pre-commit run --all-files
```

## GitHub Actions CI

Create `.github/workflows/ci.yml`:

```yaml
name: CI

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  test:
    runs-on: ubuntu-latest
    strategy:
      matrix:
        python-version: ["3.10", "3.11", "3.12"]

    steps:
      - uses: actions/checkout@v4

      - name: Set up Python ${{ matrix.python-version }}
        uses: actions/setup-python@v5
        with:
          python-version: ${{ matrix.python-version }}

      - name: Install uv
        uses: astral-sh/setup-uv@v2

      - name: Install dependencies
        run: uv pip install -e ".[dev]" --system

      - name: Lint with ruff
        run: ruff check .

      - name: Type check with mypy
        run: mypy src/

      - name: Test with pytest
        run: pytest --cov=src/my_project --cov-report=xml

      - name: Upload coverage
        uses: codecov/codecov-action@v4
        with:
          files: ./coverage.xml
```

## Post-Setup Checklist

1. [ ] Create virtual environment and activate it
2. [ ] Install development dependencies
3. [ ] Configure pre-commit hooks
4. [ ] Set up IDE/editor Python path
5. [ ] Create initial test file
6. [ ] Add LICENSE file
7. [ ] Configure CI/CD pipeline
8. [ ] Pin Python version in `.python-version`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kadajett) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
