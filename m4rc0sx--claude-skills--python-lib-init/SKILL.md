---
name: python-lib-init
description: Bootstrap a new Python LIBRARY with uv (`uv init --lib`) — distributable via PyPI, type-checked end-to-end with a `py.typed` marker, tested across a range of Python versions, strict Ruff + mypy + pytest baseline. Asks the user the MINIMUM Python version to support (floor is 3.10). Use whenever the user asks to "create / initialize / scaffold / bootstrap / start" a Python library, a package to publish on PyPI, or "una lib de uv". Use when this capability is needed.
metadata:
  author: M4RC0Sx
---

# Python library initialization (`uv init --lib`)

Apply this skill end-to-end whenever the user is bootstrapping a Python **library** — code meant to be imported by other projects and (usually) published to PyPI. Differences vs a generic package:

- The Python version is a **range** (e.g. `>=3.11`), not a single pin — libraries must work on every interpreter their declared range covers.
- Ruff's `target-version` and mypy's `python_version` are set to the **minimum**, so neither suggests syntax nor accepts patterns that only run on a newer Python.
- A `py.typed` marker ships in the wheel so downstream mypy users pick up the inline type hints (PEP 561).
- Tests run against every supported Python before each release — uv makes this a one-liner.
- The build/publish flow (`uv build` + `uv publish`) is part of the lifecycle, not an afterthought.

## Before you start

Verify with **Bash** that the user has the right tooling. **Always check current versions first** — the pinned numbers below were correct when this skill was written and drift over time. Use newer if available; do not downgrade to match this file.

```bash
# uv — bumps almost monthly
uv --version

# git and (recommended) gh
git --version && gh --version
```

If `uv` is missing:
```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
```

## Step 1 — Ask the user

Ask, one prompt with all three questions at once:

1. **Library name on PyPI** — usually lowercase with hyphens (e.g. `my-cool-lib`). The import name will be the same with hyphens turned into underscores (e.g. `import my_cool_lib`). If the user wants them to differ, note it for Step 3.
2. **Short description** — one sentence; lands in `pyproject.toml` and on PyPI.
3. **Minimum Python version** to support. **Floor is 3.10**, do not let the user go lower. Guide as of writing:

| Minimum | EOL | Pick when… |
|---|---|---|
| **3.10** | Oct 2026 (≈ now) | Legacy compat is a hard requirement. New libraries should usually skip this. |
| **3.11** | Oct 2027 | Balanced — broad ecosystem support, including big LTS distros. |
| **3.12** | Oct 2028 | **Default for new libraries**. Modern syntax (`ParamSpec`, `Self`, structural pattern matching mature), every active distro covers it. |
| **3.13** | Oct 2029 | Cutting edge — free-threading available, faster repl. Accept losing users still on 3.12. |
| **3.14** | Oct 2030 | Pre-1.0 libraries that only need to work in their own ecosystem; over-restrictive for general distribution. |

Default suggestion: **3.12** for general libraries; bump to **3.13/3.14** if the lib uses features only available there. The chosen value gets used in **five places** in Step 3 (`.python-version`, `requires-python`, `target-version`, `python_version`, and the CI matrix); the skill keeps them all in sync.

Make sure `uv` knows about the chosen versions (the minimum *and* the latest, so the dev environment runs the modern interpreter even though the lib targets the minimum):

```bash
uv python install <min-version>
uv python install <latest-version>     # only if they differ
uv python list
```

## Step 2 — Scaffold with `uv init --lib`

```bash
uv init --lib --python <min-version> <lib-name>
cd <lib-name>
```

`--lib` differs from `--package` in that the generated `__init__.py` contains a callable example rather than a script entrypoint, and the project is set up for distribution.

Expected layout:
```
<lib-name>/
├── .python-version
├── pyproject.toml
├── README.md
└── src/
    └── <import_name>/
        └── __init__.py
```

The next step **replaces** `pyproject.toml` wholesale.

## Step 3 — Replace `pyproject.toml`

Write **exactly** this, substituting:
- `<lib-name>` — PyPI distribution name (hyphens OK).
- `<import_name>` — Python import name (underscores, no hyphens).
- `<description>` — one-line description.
- `<min-version>` — e.g. `3.12`.
- `<min-py-tag>` — corresponding Ruff tag, e.g. `py312`.

```toml
[project]
name = "<lib-name>"
version = "0.1.0"
description = "<description>"
readme = "README.md"
requires-python = ">=<min-version>"
license = { text = "MIT" }
authors = [{ name = "<your name>", email = "<your email>" }]
keywords = []
classifiers = [
    "Development Status :: 3 - Alpha",
    "Intended Audience :: Developers",
    "License :: OSI Approved :: MIT License",
    "Operating System :: OS Independent",
    "Programming Language :: Python :: 3",
    "Typing :: Typed",
]
dependencies = []

[project.urls]
Homepage = "https://github.com/<user>/<lib-name>"
Repository = "https://github.com/<user>/<lib-name>"
Issues = "https://github.com/<user>/<lib-name>/issues"
Changelog = "https://github.com/<user>/<lib-name>/blob/main/CHANGELOG.md"

[build-system]
requires = ["hatchling"]
build-backend = "hatchling.build"

[tool.hatch.build.targets.wheel]
packages = ["src/<import_name>"]

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
target-version = "<min-py-tag>"
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
known-first-party = ["<import_name>"]

[tool.mypy]
strict = true
python_version = "<min-version>"
namespace_packages = true
show_error_codes = true
enable_error_code = ["ignore-without-code", "redundant-expr", "truthy-bool"]

[tool.pytest.ini_options]
minversion = "8.0"
addopts = ["-ra", "--strict-markers", "--strict-config"]
testpaths = ["tests"]
asyncio_mode = "auto"
markers = [
    "integration: tests that hit real external services",
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

**Five places must agree on the minimum Python version**:

1. `.python-version` — write the **minimum** so the local venv runs there by default; any accidental use of newer-only syntax then fails on the spot instead of slipping into a release. The CI matrix exercises the rest of the range.
2. `[project]` → `requires-python = ">=<min-version>"`.
3. `[tool.ruff]` → `target-version = "<min-py-tag>"` (e.g. `py312`).
4. `[tool.mypy]` → `python_version = "<min-version>"` (dotted form).
5. CI matrix in `.github/workflows/test.yml` (Step 9) — the `python:` matrix list must start at `<min-version>` and run up through every supported version.

Drift in any one is a real bug. Keep them in lockstep when bumping the floor later.

## Step 4 — `py.typed` marker

PEP 561: ship an empty `py.typed` next to `__init__.py` so downstream mypy/pyright pick up your inline type hints. Without it, your types are invisible to library users.

```bash
touch src/<import_name>/py.typed
```

Hatchling includes package data by default, so the marker will land in the wheel.

## Step 5 — Public API + `__version__`

Rewrite `src/<import_name>/__init__.py` to be the library's public surface:

```python
"""<lib-name> — <description>.

Public API: everything re-exported here is considered stable within a
minor version. Modules with a leading underscore (e.g. _core) are
private — they may change between any two releases without warning.
"""

from __future__ import annotations

from importlib.metadata import version as _version

__version__ = _version("<lib-name>")

# Re-export the public API. Add to __all__ what users should be able
# to `from <import_name> import ...`. Keep internal helpers in
# private submodules.

__all__ = ["__version__"]
```

`from __future__ import annotations` is recommended in every module of a library targeting Pythons below 3.12 — it treats all annotations as strings so PEP 604 unions (`int | None`), generic syntax, etc. compile on every supported version without runtime tricks. Skip it only when you genuinely need the annotation object at runtime (Pydantic v2, FastAPI, attrs auto-converters, …).

## Step 6 — Install dependencies

```bash
uv sync
```

Resolves the graph, creates `.venv/` running the minimum Python (per `.python-version`), writes `uv.lock`. Commit `uv.lock` — for **applications** it ensures reproducible deploys; for **libraries** it ensures reproducible *development* (the lockfile is not what end users install, but it pins your contributors' environment).

## Step 7 — Scaffold `tests/`

```bash
mkdir tests
touch tests/__init__.py
```

Write `tests/test_smoke.py`:

```python
"""Sanity tests — keep them passing through any refactor.

If these break, something foundational moved (entry point, package
name, version metadata, import path). Fix the cause, do not delete
the test.
"""

from __future__ import annotations


def test_package_imports() -> None:
    import <import_name>  # noqa: F401


def test_version_string() -> None:
    import <import_name>

    assert isinstance(<import_name>.__version__, str)
    assert <import_name>.__version__  # non-empty
```

Substitute `<import_name>` with the real one.

## Step 8 — VS Code settings

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

`importStrategy: "fromEnvironment"` makes both extensions use the `ruff` and `mypy` installed in `.venv/`, so editor errors match what `uv run mypy src` reports.

`.vscode/extensions.json`:

```json
{
  "recommendations": [
    "charliermarsh.ruff",
    "ms-python.mypy-type-checker",
    "ms-python.python"
  ]
}
```

## Step 9 — Multi-version testing + CI matrix

A library's value is "works on every Python in its declared range". Prove it.

**Local quick check** — loop the tests across every supported version. uv installs interpreters on demand, so this just works:

```bash
for py in 3.12 3.13 3.14; do        # adjust to your supported range
    echo "=== Python $py ==="
    uv run --python "$py" pytest || { echo "failed on $py"; break; }
done
```

**CI** — the matrix workflow that the lib must keep green before any release. Write `.github/workflows/test.yml`:

```yaml
name: test

on:
  push:
    branches: [<default-branch>]
  pull_request:
  workflow_dispatch:

jobs:
  test:
    runs-on: ubuntu-latest
    strategy:
      fail-fast: false
      matrix:
        python: ["<min-version>", "<...>", "<latest-version>"]
    steps:
      - uses: actions/checkout@v4
      - uses: astral-sh/setup-uv@v3
        with:
          enable-cache: true
      - name: Install Python ${{ matrix.python }}
        run: uv python install ${{ matrix.python }}
      - run: uv sync --frozen
      - run: uv run --python ${{ matrix.python }} ruff check .
        if: matrix.python == '<min-version>'
      - run: uv run --python ${{ matrix.python }} ruff format --check .
        if: matrix.python == '<min-version>'
      - run: uv run --python ${{ matrix.python }} mypy src
        if: matrix.python == '<min-version>'
      - run: uv run --python ${{ matrix.python }} pytest
```

The lint / format / typecheck jobs only run on the minimum Python — they assert against the floor, and running them on every interpreter is wasted CI minutes. `pytest` runs on every entry.

Fill the matrix with the actual supported versions (e.g. `["3.12", "3.13", "3.14"]`). Replace `<default-branch>` with whatever `git symbolic-ref --short HEAD` reports after `git init` (typically `main` or `master`) — do not hard-code one, it has to match the user's actual default branch.

## Step 10 — `README.md`, `LICENSE`, `CHANGELOG.md`

`uv init --lib` already wrote a stub `README.md`; expand it with the canonical sections:

```markdown
# <lib-name>

<description>

## Installation

\```bash
pip install <lib-name>
# or with uv:
uv add <lib-name>
\```

## Quick start

\```python
from <import_name> import ...
\```

## Compatibility

Supported Python versions: <min-version>+ (tested on the matrix in `.github/workflows/test.yml`).

## License

MIT — see [LICENSE](LICENSE).
```

Write `LICENSE` (MIT is the default if the user has no preference; offer to switch to Apache-2.0 or BSD if they ask).

Write `CHANGELOG.md` starting with an "Unreleased" section in [Keep a Changelog](https://keepachangelog.com/) format:

```markdown
# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

### Added
- Initial scaffold.
```

## Step 11 — Cheat sheet

Mention these to the user at the end of setup:

```bash
# add a runtime dep (goes into [project].dependencies — DO NOT pin
# to an exact version, use a lower bound: `uv add "httpx>=0.27"`)
uv add <pkg>

# add a dev dep (lands in [dependency-groups].dev)
uv add --dev <pkg>

# run anything inside the venv
uv run pytest
uv run mypy src
uv run ruff check .
uv run ruff format .

# test on a specific Python without changing your venv
uv run --python 3.12 pytest

# build wheel + sdist into dist/
uv build

# publish to PyPI (set UV_PUBLISH_TOKEN to a PyPI API token)
uv publish

# publish to TestPyPI for a dry-run
uv publish --publish-url https://test.pypi.org/legacy/
```

**Release workflow** (when the lib is ready to ship):

1. Update `version` in `pyproject.toml`.
2. Move "Unreleased" entries in `CHANGELOG.md` under a `[X.Y.Z] — YYYY-MM-DD` heading.
3. Commit: `chore(release): vX.Y.Z`.
4. Tag: `git tag -a vX.Y.Z -m "vX.Y.Z" && git push --tags`.
5. `uv build && uv publish`.
6. `gh release create vX.Y.Z --generate-notes` to mirror the changelog on GitHub.

## Step 12 — Verify

```bash
uv run ruff check .
uv run ruff format --check .
uv run mypy src
uv run pytest

# build sanity — should produce dist/<lib-name>-0.1.0-py3-none-any.whl and .tar.gz
uv build
ls dist/

# (optional but recommended) prove the floor works locally
uv run --python <min-version> pytest
```

All steps must succeed. If `mypy` reports "no module named `<import_name>`", re-run `uv sync` — the venv was not up to date with the new layout.

## Step 13 — First commit

```bash
# Pull the canonical Python.gitignore (overwrites the one uv init
# scaffolded). github/gitignore is the upstream source of truth and
# tracks new tools/caches as they appear.
curl -sLo .gitignore https://raw.githubusercontent.com/github/gitignore/main/Python.gitignore

git init
git add -A
git commit -m "chore: initial library scaffold"
```

`git init` (no `-b` flag) honours `init.defaultBranch` from the user's git config — works for `main`, `master`, or anything else. Whichever branch this leaves you on is the value to write into `branches: [<default-branch>]` in the workflow from Step 9 before pushing.

If a `gh` remote is desired, ask the user before creating it.

## Anti-patterns this skill is built to prevent

- **Pinning runtime deps to exact versions** in `[project].dependencies`. Libraries should declare lower bounds (`httpx>=0.27`) and, only when necessary, upper bounds (`httpx>=0.27,<1.0`) — pinning forces users into upgrade lockstep with you, which is rude.
- **Putting dev tools in `[project].dependencies`**. `pytest`, `mypy`, `ruff` live in `[dependency-groups].dev`; otherwise every user of your library installs them transitively.
- **Skipping `py.typed`** when the library has type hints. Without it, downstream mypy ignores your types and your users get `Any` everywhere.
- **Mismatched Python version pins** across `.python-version`, `requires-python`, `target-version`, `python_version`, and the CI matrix. Drift in any one means a release that mypy approves but actually crashes on the floor interpreter.
- **`from __future__ import annotations`** omitted on libraries targeting Pythons below 3.12 that use PEP 604 unions or generic syntax in annotations.
- **Removing or renaming a public name without bumping the major version.** Library users depend on every name in `__all__`. Breaking changes need a major bump and a clear CHANGELOG entry.
- **`select = ["ALL"]`** in Ruff. Behaviour changes silently on every Ruff release. Use the curated `extend-select`.
- **`# type: ignore`** without a code. Defeats the point of mypy strict.
- **Naive `datetime`**. `DTZ` already errors; use `datetime.now(UTC)`, never `datetime.utcnow()`.
- **Relative imports**. `ban-relative-imports = "all"` is set; use absolute imports.
- **Releasing without running the CI matrix**. The whole point of the lib is "works on every supported Python" — proof, not faith.

## Things this skill does NOT cover

- **Stable API design** — what to put in `__all__`, when to break it. Domain-specific.
- **Sphinx / mkdocs docs**. Pick a generator per project; not every library needs one beyond the README.
- **`tox`, `nox`, `hatch envs`** — the `uv run --python X.Y` loop and the CI matrix above cover most cases. Wire one of these only if the multi-version surface gets complex (e.g. compiled extensions, Python-version-specific code paths).
- **Trusted Publishing** to PyPI via GitHub Actions OIDC — recommended for production libraries, but configuring it lives in `.github/workflows/release.yml` and the PyPI project settings, out of scope for setup.
- **Pre-commit hooks**. Editor format-on-save + `uv run …` before pushing is enough at this stage.

## When this skill goes stale

- The "Pick when…" matrix needs to move as Python releases age. Check https://devguide.python.org/versions/ for current EOL dates; new minor every October.
- `uv build` / `uv publish` are relatively new (uv ≥ 0.4). If they were superseded by another command, follow `uv help`.
- Hatchling vs other build backends (`flit_core`, `pdm-backend`, `setuptools.build_meta`) — hatchling is the modern default but if uv switches what it scaffolds, audit this skill.
- Ruff adds rule categories every few releases. The `extend-select` list deliberately lists letters; if a new category like `DOC` is enabled across your other projects, add it explicitly here too.
- If PEP 735 (`[dependency-groups]`) gets replaced or extended (e.g. groups for docs, lint, type-check separately), revisit.

---
> Source: [M4RC0Sx/claude-skills](https://github.com/M4RC0Sx/claude-skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-22 -->
