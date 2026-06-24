---
name: poetry
description: Manage Python projects with Poetry for dependency management, packaging, and publishing. Use when users want to create pyproject.toml files, add/remove dependencies, configure virtual environments, build packages, or publish to PyPI. Generates working configuration files and shell commands. Use when this capability is needed.
metadata:
  author: nealepetrillo
---

# Poetry Python Package Manager Skill

Manage Python projects using Poetry for dependency management, virtual environments, packaging, and publishing. Follow Python packaging standards (PEP 621, PEP 735) for maximum compatibility.

**Version**: Poetry 2.x | Requires: Python 3.9+

## Workflows

Determine which workflow to follow based on the user's request:

**Starting a new project?** → Follow "New Project Setup" below
**Managing dependencies?** → Follow "Dependency Management" below
**Building or publishing?** → Follow "Build & Publish" below
**Configuring pyproject.toml?** → See `references/pyproject-toml.md` for complete reference
**Need a specific CLI command?** → See `references/cli-reference.md` for all commands

### New Project Setup

1. Determine project type:
   - **Library/application to distribute** → Use package mode (default)
   - **Dependency management only** → Use non-package mode (`package-mode = false`)

2. Create the project:
   ```bash
   poetry new my-package        # New directory with scaffolding
   poetry init                  # Initialize in existing directory
   ```

3. Generate a `pyproject.toml` with at minimum:
   ```toml
   [build-system]
   requires = ["poetry-core>=2.0.0,<3.0.0"]
   build-backend = "poetry.core.masonry.api"

   [project]
   name = "my-package"
   version = "0.1.0"
   description = "A Python package"
   readme = "README.md"
   requires-python = ">=3.9"
   license = "MIT"
   authors = [{ name = "Your Name", email = "you@example.com" }]
   dependencies = []
   ```

   For non-package mode, add `[tool.poetry]` with `package-mode = false` and omit `name`/`version` from `[project]`.

   See `references/pyproject-toml.md` for full configuration options including classifiers, entry points, URLs, and tool-specific sections (pytest, ruff, mypy, etc.).

4. Install and create the virtual environment:
   ```bash
   poetry install
   ```

### Dependency Management

Use PEP 440 constraints in `[project.dependencies]` for standard dependencies. Use `[tool.poetry.dependencies]` only for Poetry-specific features (caret `^`/tilde `~` operators, git/path sources).

```bash
poetry add requests                    # Add to main dependencies
poetry add "requests>=2.28.0"          # With version constraint
poetry add pytest --group test         # Add to dependency group
poetry add ./local-package             # Add local package
poetry add git+https://github.com/...  # Add from git
poetry remove requests                 # Remove dependency
poetry update                          # Update all to latest
poetry lock                            # Regenerate lock file
```

Organize dependencies into groups:
- **Main** → `[project.dependencies]` — always installed
- **Optional groups** → `[project.optional-dependencies]` or `[tool.poetry.group.<name>.dependencies]` — installed with `--with`

See `references/pyproject-toml.md` for version constraint syntax, environment markers, git/path/URL dependencies, and extras.
See `references/cli-reference.md` for all `add`, `remove`, `update`, `install`, and `sync` options.

### Build & Publish

1. Validate configuration: `poetry check`
2. Build: `poetry build`
3. Configure credentials: `poetry config pypi-token.pypi <token>`
4. Publish: `poetry publish`

For private repositories, configure the repository first:
```bash
poetry config repositories.private https://pypi.example.com/simple/
poetry publish --repository private
```

See `references/cli-reference.md` for all build/publish/config options.

## Best Practices

1. **Commit poetry.lock** for applications (reproducible builds)
2. **Consider not committing poetry.lock** for libraries (let users resolve)
3. **Use dependency groups** to organize dev, test, docs dependencies
4. **Pin major versions** with `>=X.Y,<X+1` for stability
5. **Run `poetry check`** before commits to validate configuration
6. **Use `poetry sync`** in CI for exact reproducibility

## Pre-commit Hooks

```yaml
# .pre-commit-config.yaml
repos:
  - repo: https://github.com/python-poetry/poetry
    rev: '2.0.0'
    hooks:
      - id: poetry-check
      - id: poetry-lock
        args: ["--check"]
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/nealepetrillo) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
