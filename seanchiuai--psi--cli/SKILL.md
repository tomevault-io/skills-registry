---
name: cli
description: Click-based CLI entry points — psi new and psi run commands Use when this capability is needed.
metadata:
  author: seanchiuai
---

# CLI

Click command group in `src/psi/cli.py`. Two commands: `psi new` and `psi run`.

## Overview

Entry point configured in `pyproject.toml`:
```toml
[project.scripts]
psi = "psi.cli:cli"
```

Also runnable as `python -m psi` via `__main__.py`.

## Commands

### `psi new <directory> "<description>"`

Scaffolds a new project:
- `directory` — `click.Path(file_okay=False)` — target path (must not exist or be empty)
- `description` — app description passed to Claude for template filling

Calls `scaffold.scaffold_project()`. Fails with `click.ClickException` if directory exists and is non-empty.

### `psi run [directory] [options]`

Runs the orchestrator loop:
- `directory` — `click.Path(exists=True, file_okay=False)` — project root (default: `"."`)
- `--max-iterations, -m` — `type=int, default=30` — max iterations per phase
- `--timeout, -t` — `type=int, default=600` — seconds per iteration
- `--no-weave` — `is_flag=True` — disable Weave tracing

Creates a `Psi` instance and calls `run()`. Exit code 0 on success, 1 on failure.

## Patterns

### Lazy Imports

Both commands use lazy imports inside the command function to avoid loading heavy modules (orchestrator, scaffold) at CLI parse time:

```python
@cli.command()
def new(directory, description):
    from psi.scaffold import scaffold_project  # lazy
    scaffold_project(...)
```

### Exit Handling

`psi run` uses `raise SystemExit(code)` instead of `sys.exit()` — works cleanly with Click's exception handling.

## Related Files

- `src/psi/cli.py` — Click commands
- `src/psi/__main__.py` — `python -m psi` entry
- `src/psi/scaffold.py` — Called by `psi new`
- `src/psi/orchestrator.py` — Called by `psi run`
- `pyproject.toml` — `[project.scripts]` entry

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/seanchiuai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
