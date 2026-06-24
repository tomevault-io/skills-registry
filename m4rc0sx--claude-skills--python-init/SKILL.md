---
name: python-init
description: Bootstrap a new Python package with uv + the canonical strict baseline — Ruff with curated extend-select, mypy in strict mode with extra error codes, pytest baseline (pytest-asyncio auto, pytest-cov, pytest-mock, pytest-randomly, time-machine), src-layout from `uv init --package`. Asks the user which Python version to target. Use whenever the user asks to "create / initialize / scaffold / bootstrap / start" a new Python project, package, CLI, or internal tool. For Python LIBRARIES meant to be published on PyPI use `python-lib-init` instead; for FastAPI services use `python-fastapi-init` instead. Use when this capability is needed.
metadata:
  author: M4RC0Sx
---

# Python project initialization (generic package)

Apply this skill end-to-end whenever bootstrapping a new Python package — a CLI, an analysis tool, an internal package, anything that ships as an importable package but isn't aimed at PyPI as a public library.

For **public libraries** (those meant to be published on PyPI and consumed by other projects), use `python-lib-init` instead — it ships a multi-Python CI matrix, build/publish flow, classifiers, `LICENSE`, `CHANGELOG.md`, and the rest of the distribution scaffolding that this skill deliberately skips.

For **FastAPI services**, use `python-fastapi-init` instead; the deltas there are non-trivial enough to keep them separate.

The configs below are designed to work together. Deviations need to be justified in the PR that introduces them.

## Before you start

Verify with **Bash** that the user has the right tooling. **Always check current versions first** — the pinned numbers below were correct when this skill was written and drift over time. If `uv` or Python have moved on, use the newer versions; do not downgrade to match this file.

```bash
# uv — bumps almost monthly; >= 0.5 has [dependency-groups] + uv python install
uv --version

# git and (recommended) gh
git --version && gh --version
```

If `uv` is missing:
```bash
# macOS / Linux installer (official, single static binary)
curl -LsSf https://astral.sh/uv/install.sh | sh
```

## Step 1 — Ask the user

Ask, one short prompt with both questions at once:

1. **Project / package name** (e.g. `myapp` — will be importable as `import myapp`). Must be a valid Python identifier (snake_case, no hyphens).
2. **Python version**. Quick guide as of writing:

| Version | Status | Pick when… |
|---|---|---|
| **3.14** | Latest stable (Oct 2025) | Default. Faster start-up, optional free-threading, better error messages. |
| **3.13** | Mature stable (Oct 2024) | A critical dependency hasn't published 3.14 wheels yet. |
| **3.12** | Maintenance | You're integrating with an existing project still on 3.12. |
| **≤ 3.11** | EOL or near-EOL | Avoid unless required by an external constraint. |

Default to **3.14** unless the user says otherwise.

Make sure `uv` knows about the chosen version:
```bash
uv python install <version>     # no-op if already installed
uv python list                  # confirm the version shows up
```

## Step 2 — Scaffold with `uv init --package`

This creates a packaged project with src-layout (`src/<name>/__init__.py`), a build system (hatchling), `.python-version`, and a starter `pyproject.toml`. The `--package` flag is what distinguishes "real package" from "just some scripts".

```bash
uv init --package --python <version> <package-name>
cd <package-name>
```

Expected layout right after init:
```
<package-name>/
├── .python-version
├── pyproject.toml
├── README.md
└── src/
    └── <package_name>/
        └── __init__.py
```

The next step **replaces** `pyproject.toml` wholesale — what `uv init` generated is a starting point, but the strict configs need to land deterministically.

## Step 3 — Replace `pyproject.toml`

Write **exactly** this, substituting `<package_name>`, `<python-version>` (e.g. `3.14`), and `<py-tag>` (e.g. `py314`):

```toml
[project]
name = "<package_name>"
version = "0.1.0"
description = ""
readme = "README.md"
requires-python = ">=<python-version>"
dependencies = []

[build-system]
requires = ["hatchling"]
build-backend = "hatchling.build"

[dependency-groups]
dev = [
    "ruff>=0.7",
    "mypy>=1.13",
    "pytest>=8",
    "pytest-cov>=5",
    "pytest-asyncio>=0.24",
    "pytest-mock>=3.14",
    "pytest-randomly>=3.15",
    "time-machine>=2.15",
]

[tool.ruff]
target-version = "<py-tag>"
line-length = 88

[tool.ruff.lint]
extend-select = [
    "A",     # flake8-builtins
    "ARG",   # flake8-unused-arguments
    "ASYNC", # flake8-async
    "B",     # flake8-bugbear
    "BLE",   # flake8-blind-except
    "C4",    # flake8-comprehensions
    "C90",   # mccabe
    "DTZ",   # flake8-datetimez
    "E",     # pycodestyle errors
    "EM",    # flake8-errmsg
    "FBT",   # flake8-boolean-trap
    "FLY",   # flynt
    "FURB",  # refurb
    "G",     # flake8-logging-format
    "I",     # isort
    "ICN",   # flake8-import-conventions
    "ISC",   # flake8-implicit-str-concat
    "INT",   # flake8-gettext
    "LOG",   # flake8-logging
    "N",     # pep8-naming
    "PERF",  # perflint
    "PGH",   # pygrep-hooks
    "PIE",   # flake8-pie
    "PL",    # pylint
    "PTH",   # flake8-use-pathlib
    "Q",     # flake8-quotes
    "RET",   # flake8-return
    "RSE",   # flake8-raise
    "RUF",   # ruff-specific rules
    "S",     # flake8-bandit
    "SIM",   # flake8-simplify
    "SLF",   # flake8-self
    "SLOT",  # flake8-slots
    "T10",   # flake8-debugger
    "T20",   # flake8-print
    "TC",    # flake8-type-checking
    "TID",   # flake8-tidy-imports
    "TRY",   # tryceratops
    "UP",    # pyupgrade
    "W",     # pycodestyle warnings
    "YTT",   # flake8-2020
]
ignore = [
    "PLR0913", # too many arguments — sometimes unavoidable
    "PLR2004", # magic value comparison — too many false positives
    "TRY003",  # long exception messages — sometimes clearer inline
]

[tool.ruff.lint.per-file-ignores]
"tests/**/*.py" = [
    "S101",    # `assert` is how pytest works
    "SLF001",  # tests sometimes peek at private state
]

[tool.ruff.lint.flake8-tidy-imports]
ban-relative-imports = "all"

[tool.ruff.lint.isort]
force-single-line = true
lines-between-types = 1
lines-after-imports = 2
known-first-party = ["<package_name>"]

[tool.mypy]
strict = true
python_version = "<python-version>"
namespace_packages = true
show_error_codes = true
enable_error_code = ["ignore-without-code", "redundant-expr", "truthy-bool"]

[tool.pytest.ini_options]
minversion = "8.0"
addopts = ["-ra", "--strict-markers", "--strict-config"]
testpaths = ["tests"]
asyncio_mode = "auto"
markers = [
    "integration: tests that hit real external services (DB, Redis, HTTP)",
    "slow: tests > 1s",
]

[tool.coverage.run]
source = ["src"]
branch = true

[tool.coverage.report]
exclude_lines = [
    "pragma: no cover",
    "if TYPE_CHECKING:",
    "raise NotImplementedError",
    "@overload",
]
show_missing = true
skip_covered = true
```

**Places that must agree on the Python version** — change all of them when bumping the version later, or any one drifts:
1. `.python-version`
2. `[project]` → `requires-python`
3. `[tool.ruff]` → `target-version` (uses the `py<NN>` tag, e.g. `py314`)
4. `[tool.mypy]` → `python_version` (uses the dotted form, e.g. `3.14`)

Non-negotiables baked in: `strict = true` on mypy (with the three extra error codes that catch real bugs), the curated `extend-select` instead of `select = ["ALL"]` (which changes behaviour on every Ruff release), and a short `ignore` list whose every entry has a one-line justification.

## Step 4 — Install dependencies

```bash
uv sync
```

This reads `pyproject.toml`, resolves the dependency graph, creates `.venv/`, writes `uv.lock`, and installs both the runtime deps (none yet) and the default dev group. Commit `uv.lock` — it pins the entire transitive graph and is what makes installs reproducible across machines.

## Step 5 — `py.typed` marker

PEP 561: ship an empty `py.typed` next to `__init__.py` so that *if* any other project ever imports this package, their mypy/pyright picks up the inline type hints rather than seeing `Any`. The file is free (an empty marker) and removes a class of "I added types but downstream still sees Any" surprises that nobody remembers to fix later.

```bash
touch src/<package_name>/py.typed
```

Hatchling ships package data by default, so the marker will land in the wheel if/when the project is ever distributed.

## Step 6 — Scaffold `tests/`

`uv init --package` does not create `tests/`. Do it now so the first `pytest` run finds something:

```bash
mkdir tests
touch tests/__init__.py
```

Write `tests/test_smoke.py`:

```python
"""Sanity tests — keep them passing through any refactor.

If these break, something foundational moved (entry point, package name,
import path). Fix the cause, do not delete the test.
"""

from __future__ import annotations


def test_package_imports() -> None:
    # Replace <package_name> with the actual import path.
    import <package_name>  # noqa: F401
```

Substitute `<package_name>` with the real one.

## Step 7 — VS Code settings

Write `.vscode/settings.json` (commit it):

```json
{
  "[python]": {
    "editor.defaultFormatter": "charliermarsh.ruff",
    "editor.formatOnSave": true,
    "editor.codeActionsOnSave": {
      "source.fixAll.ruff": "explicit",
      "source.organizeImports.ruff": "explicit"
    }
  },
  "python.formatting.provider": "none",
  "ruff.importStrategy": "fromEnvironment",
  "mypy-type-checker.importStrategy": "fromEnvironment",
  "python.defaultInterpreterPath": "${workspaceFolder}/.venv/bin/python"
}
```

`importStrategy: "fromEnvironment"` makes the extensions use the `ruff` and `mypy` installed in the project's `.venv/` rather than VS Code's bundled versions — keeps editor errors in lockstep with what CI will report.

Recommended `.vscode/extensions.json`:

```json
{
  "recommendations": [
    "charliermarsh.ruff",
    "ms-python.mypy-type-checker",
    "ms-python.python"
  ]
}
```

## Step 8 — Cheat sheet

Useful commands the user will run constantly. Mention them at the end of setup:

```bash
# add a runtime dep
uv add <pkg>

# add a dev dep (lands in [dependency-groups].dev)
uv add --dev <pkg>

# run something in the project's venv without activating it
uv run python -m <module>
uv run pytest
uv run mypy src
uv run ruff check .
uv run ruff format .

# sync the venv after pulling new commits
uv sync

# bump a single dep
uv lock --upgrade-package <pkg>
```

## Step 9 — Verify

Run, in this order:

```bash
uv run ruff check .
uv run ruff format --check .
uv run mypy src
uv run pytest
```

Each must succeed. The smoke test from Step 5 keeps `pytest` from exiting with "no tests collected" (which `--strict-config` would otherwise turn into a failure if you ever add `--strict` flags later).

If `mypy` warns "no module named `<package_name>`", the `.venv/` was not synced with the new package layout — re-run `uv sync`.

## Step 10 — First commit

```bash
# Pull the canonical Python.gitignore (overwrites the one uv init
# scaffolded). github/gitignore is the upstream source of truth and
# tracks new tools/caches as they appear.
curl -sLo .gitignore https://raw.githubusercontent.com/github/gitignore/main/Python.gitignore

git init
git add -A
git commit -m "chore: initial package scaffold"
```

`git init` (no `-b` flag) honours `init.defaultBranch` from the user's git config — works for `main`, `master`, or anything else without forcing a rename.

If a `gh` remote is desired, ask the user before creating it.

## Anti-patterns this skill is built to prevent

- **`select = ["ALL"]`** in Ruff. Behaviour changes silently on every Ruff release, and you ship a wall of false positives. Use the explicit `extend-select` list.
- **Adding rules to `ignore` without a one-line justification** of *why*. An undocumented ignore is debt.
- **`# type: ignore`** without a code (`# type: ignore[<code>]`). `ignore-without-code` already errors; do not weaken it.
- **`# type: ignore` inside the package** for things you control. Fix the type instead; only use ignores at the boundary with un-typed third-party code, and even then prefer adding a `[[tool.mypy.overrides]] module = "x.*"` block.
- **Naive `datetime`**. `DTZ` already errors; use `datetime.now(UTC)`, never `datetime.utcnow()` (deprecated in 3.12).
- **Relative imports**. `flake8-tidy-imports` is configured with `ban-relative-imports = "all"` — use absolute imports always.
- **Globally relaxing mypy strictness** to silence one file. Use `[[tool.mypy.overrides]] module = "<that-module>"` and `disallow_untyped_defs = false`, with a comment.
- **Committing without re-running `uv sync`** after editing `pyproject.toml`. `uv.lock` must move in the same commit as the spec it locks.
- **Skipping the smoke test**. Looks pointless until the day you rename the package and discover at deploy time that nothing imports anymore.

## Things this skill does NOT cover

- **CI**. Adding a GitHub Actions workflow is per-project. The pieces it needs are already in the scripts above (`uv sync --frozen`, `uv run ruff check .`, `uv run mypy src`, `uv run pytest`).
- **Pre-commit hooks**. Use the editor's format-on-save and run `uv run <…>` before pushing. Add Lefthook or pre-commit per project if you want a belt over the suspenders.
- **Publishing to PyPI**. `uv build` + `uv publish` works; out of scope for setup.
- **Optional HTTP / async test deps** (`respx` for httpx, `responses` for requests, `aioresponses` for aiohttp, `testcontainers` for real Postgres/Redis). Add them per project once the relevant runtime dep lands.
- **Pydantic settings**, **SQLAlchemy**, **httpx**, etc. — runtime dependencies, picked per project. The `python-fastapi-init` skill wires those for HTTP services specifically.

## When this skill goes stale

- Check Python's release cycle (https://devguide.python.org/versions/) — the "Pick when…" table needs to move when 3.15 lands stable (Oct 2026 expected).
- `uv` ships fast; `[dependency-groups]` (PEP 735) might be replaced by another standard or change semantics. Verify with `uv help` if the install command behaves unexpectedly.
- Ruff adds rule categories every few releases. The `extend-select` list deliberately lists letters, not "ALL", so additions are opt-in — but if a new category like `DOC` matters to you, add it explicitly here.
- If mypy's `strict = true` gains a new sub-flag that's worth enabling explicitly (or that becomes the default), audit and update.

---
> Source: [M4RC0Sx/claude-skills](https://github.com/M4RC0Sx/claude-skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-22 -->
