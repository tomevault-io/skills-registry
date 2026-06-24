---
name: python-project-prep
description: Prepare Python projects with professional structure, documentation, and tooling. Use when starting a new Python project, setting up a repository, creating project documentation (README, CONTRIBUTING), configuring development tools (pytest, ruff, mypy, pre-commit), or establishing CI/CD pipelines. Triggers on requests to "start a Python project", "set up a repo", "create project structure", "initialize a package", "prepare development environment", or any Python project scaffolding needs. Use when this capability is needed.
metadata:
  author: camauger
---

# Python Project Preparation

Comprehensive workflow for initializing Python projects with modern best practices.

## Workflow Overview

1. **Clarify requirements** → Understand project scope and constraints
2. **Plan architecture** → Design structure appropriate to project type
3. **Initialize structure** → Create directories and base files
4. **Configure tooling** → Set up dev tools, linting, testing
5. **Document** → README, CONTRIBUTING, API docs as needed
6. **Verify setup** → Test that everything works

## Step 1: Clarify Requirements

Gather essential information before scaffolding:

| Question | Why it matters |
|----------|----------------|
| Project type? (CLI, library, web app, script collection) | Determines structure and entry points |
| Distribution target? (PyPI, internal, single-use) | Affects packaging and versioning |
| Python version constraints? | Influences syntax and dependency choices |
| Key dependencies? | May require specific project patterns |
| Team size / collaboration needs? | Determines CI/CD and contribution workflow complexity |

Skip questions with obvious answers from context.

## Step 2: Plan Architecture

### Project Type Patterns

**Library/Package** (for PyPI or internal distribution):
```
project-name/
├── src/project_name/      # Source code (src layout)
│   ├── __init__.py
│   └── core.py
├── tests/
├── docs/
├── pyproject.toml
├── README.md
└── LICENSE
```

**CLI Application**:
```
project-name/
├── src/project_name/
│   ├── __init__.py
│   ├── cli.py             # Entry point
│   └── commands/
├── tests/
├── pyproject.toml
└── README.md
```

**Web Application** (FastAPI/Flask):
```
project-name/
├── src/project_name/
│   ├── __init__.py
│   ├── main.py            # App entry
│   ├── api/
│   ├── models/
│   └── services/
├── tests/
├── alembic/               # If using DB migrations
├── pyproject.toml
└── README.md
```

**Script Collection** (utilities, automation):
```
project-name/
├── scripts/
│   ├── script_one.py
│   └── script_two.py
├── shared/                # Common utilities
├── requirements.txt       # Or pyproject.toml
└── README.md
```

See `references/project-templates.md` for detailed templates with all standard files.

## Step 3: Initialize Structure

### Use the Init Script

Run the bundled initialization script for quick setup:

```bash
python scripts/init_project.py <project-name> --type <library|cli|webapp|scripts>
```

The script creates the directory structure, `pyproject.toml`, and base files.

### Manual Initialization

If customization is needed, create structure manually:

```bash
mkdir -p src/project_name tests docs
touch src/project_name/__init__.py
touch tests/__init__.py
touch pyproject.toml README.md LICENSE
```

## Step 4: Configure Tooling

### pyproject.toml (Modern Standard)

All configuration in one file. See `references/tool-configs.md` for complete examples.

**Minimal pyproject.toml**:
```toml
[project]
name = "project-name"
version = "0.1.0"
description = "Brief description"
requires-python = ">=3.10"
dependencies = []

[project.optional-dependencies]
dev = ["pytest>=8.0", "ruff>=0.4", "mypy>=1.10"]

[build-system]
requires = ["hatchling"]
build-backend = "hatchling.build"
```

### Essential Dev Tools

| Tool | Purpose | Config location |
|------|---------|-----------------|
| **ruff** | Linting + formatting (replaces flake8, black, isort) | `[tool.ruff]` |
| **mypy** | Type checking | `[tool.mypy]` |
| **pytest** | Testing | `[tool.pytest.ini_options]` |
| **pre-commit** | Git hooks | `.pre-commit-config.yaml` |

### Recommended Tool Configuration

Add to `pyproject.toml`:

```toml
[tool.ruff]
line-length = 88
target-version = "py310"

[tool.ruff.lint]
select = ["E", "F", "I", "UP", "B", "SIM"]

[tool.mypy]
python_version = "3.10"
strict = true

[tool.pytest.ini_options]
testpaths = ["tests"]
addopts = "-v --tb=short"
```

### Pre-commit Hooks

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
```

Install with: `pre-commit install`

## Step 5: Document

### README.md Structure

```markdown
# Project Name

Brief description (1-2 sentences).

## Installation

\`\`\`bash
pip install project-name
\`\`\`

## Quick Start

\`\`\`python
# Minimal working example
\`\`\`

## Usage

[Key features and examples]

## Development

\`\`\`bash
pip install -e ".[dev]"
pre-commit install
pytest
\`\`\`

## License

[License type]
```

### Additional Docs (as needed)

- **CONTRIBUTING.md** → For open source or team projects
- **CHANGELOG.md** → For versioned releases
- **docs/** → For complex APIs (use mkdocs or sphinx)

## Step 6: Verify Setup

Run these checks after initialization:

```bash
# Install in development mode
pip install -e ".[dev]"

# Verify linting works
ruff check .
ruff format --check .

# Verify type checking
mypy src/

# Verify tests run
pytest

# Verify package imports
python -c "import project_name"
```

## Quick Reference

### Common Commands

```bash
# Create virtual environment
python -m venv .venv && source .venv/bin/activate

# Install with dev dependencies
pip install -e ".[dev]"

# Run all quality checks
ruff check . && ruff format --check . && mypy src/ && pytest

# Build distribution
python -m build

# Upload to PyPI (test)
twine upload --repository testpypi dist/*
```

### Version Bumping

Use semantic versioning: `MAJOR.MINOR.PATCH`
- MAJOR: Breaking changes
- MINOR: New features (backward compatible)
- PATCH: Bug fixes

## Conditional Workflows

**Starting from scratch?** → Follow steps 1-6 sequentially

**Adding tooling to existing project?** → Jump to Step 4, adapt configs to existing structure

**Just need documentation?** → Jump to Step 5, use templates from references

**Need CI/CD setup?** → See `references/tool-configs.md` for GitHub Actions templates

---
> Source: [camauger/dev-skills](https://github.com/camauger/dev-skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-22 -->
