---
name: python-uv-ci
description: > Use when this capability is needed.
metadata:
  author: datarecce
---

# Python CI with uv

## GitHub Actions Steps

### Setup and Install

```yaml
- uses: actions/checkout@v4

- name: Install uv
  uses: astral-sh/setup-uv@v7
  with:
    enable-cache: true
    python-version: "{PYTHON_VERSION}"

- name: Create venv and install dependencies
  run: |
    uv venv
    uv pip install {DEPENDENCIES}
```

### Running Commands

After setup, use `uv run` to execute commands in the venv:

```yaml
- name: Run command
  run: uv run {COMMAND}
```

## Variables

| Variable | Description | Default |
|----------|-------------|---------|
| `{PYTHON_VERSION}` | Python version | `3.12` |
| `{DEPENDENCIES}` | Space-separated packages | - |
| `{COMMAND}` | Command to run | - |

## Best Practices

- `enable-cache: true` caches `~/.cache/uv` automatically
- `uv venv` creates `.venv` in workspace (isolated from system)
- Use `uv run` to execute commands in the venv without explicit activation
- `setup-uv@v7` handles Python installation via `python-version` input

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/datarecce) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
