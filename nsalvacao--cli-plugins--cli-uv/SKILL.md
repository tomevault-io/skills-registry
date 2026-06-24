---
name: cli-uv
description: >- Use when this capability is needed.
metadata:
  author: nsalvacao
---

# uv CLI Reference

Expert command reference for **uv** v0.9.28.

- **54** commands (6 with subcommands)
- **1668** command flags + **16** global flags
- **0** usage examples
- Max nesting depth: 1

## When to Use

This skill applies when:
- Constructing or validating `uv` commands
- Looking up flags, options, or subcommands
- Troubleshooting `uv` invocations or errors
- Needing correct syntax for `uv` operations

## Prerequisites

Ensure `uv` is installed and available on PATH.

## Quick Reference

| Command | Description |
| --- | --- |
| `uv add` | Add dependencies to the project |
| `uv auth` | Manage authentication |
| `uv build` | Build Python packages into source distributions and wheels |
| `uv cache` | Manage uv's cache |
| `uv export` | Export the project's lockfile to an alternate format |
| `uv format` | Format Python code in the project |
| `uv help` | Display documentation for a command |
| `uv init` | Create a new project |
| `uv lock` | Update the project's lockfile |
| `uv pip` | Manage Python packages with a pip-compatible interface |
| `uv publish` | Upload distributions to an index |
| `uv python` | Manage Python versions and installations |
| `uv remove` | Remove dependencies from the project |
| `uv run` | Run a command or script |
| `uv self` | Manage the uv executable |
| `uv sync` | Update the project's environment |
| `uv tool` | Run and install commands provided by Python packages |
| `uv tree` | Display the project's dependency tree |
| `uv venv` | Create a virtual environment |
| `uv version` | Read or update the project's version |

### Global Flags

| Flag | Short | Type | Description |
| --- | --- | --- | --- |
| `--allow-insecure-host` | `` | string | Allow insecure connections to a host [env: UV_INSECURE_HOST=] |
| `--cache-dir` | `` | string | Path to the cache directory [env: UV_CACHE_DIR=] |
| `--color` | `` | string | Control the use of color in output [possible values: auto, always, never] |
| `--config-file` | `` | string | The path to a `uv.toml` file to use for configuration [env: UV_CONFIG_FILE=] |
| `--directory` | `` | string | Change to the given directory prior to running the command [env: UV_WORKING_DIR=] |
| `--help` | `-h` | bool | Display the concise help for this command |
| `--managed-python` | `` | bool | Require use of uv-managed Python versions [env: UV_MANAGED_PYTHON=] |
| `--native-tls` | `` | bool | Whether to load TLS certificates from the platform's native store [env: UV_NATIVE_TLS=] |
| `--no-cache` | `-n` | string | Avoid reading from or writing to the cache, instead using a temporary directory for the |
| `--no-config` | `` | string | Avoid discovering configuration files (`pyproject.toml`, `uv.toml`) [env: UV_NO_CONFIG=] |
| `--no-managed-python` | `` | bool | Disable use of uv-managed Python versions [env: UV_NO_MANAGED_PYTHON=] |
| `--no-progress` | `` | bool | Hide all progress outputs [env: UV_NO_PROGRESS=] |
| `--no-python-downloads` | `` | bool | Disable automatic downloads of Python. [env: "UV_PYTHON_DOWNLOADS=never"] |
| `--offline` | `` | bool | Disable network access [env: UV_OFFLINE=] |
| `--project` | `` | string | Discover a project in the given directory [env: UV_PROJECT=] |
| `--version` | `-V` | bool | Display the uv version |

## Command Overview


### Commands

`add`, `build`, `export`, `format`, `help`, `init`, `lock`, `publish`, `remove`, `run`, `sync`, `tree`, `venv`, `version`

### Command Groups

`auth`, `cache`, `pip`, `python`, `self`, `tool`

## Common Usage Patterns


## Detailed References

For complete command documentation including all flags and subcommands:
- **Full command tree:** see `references/commands.md`
- **All usage examples:** see `references/examples.md`

## Troubleshooting

- Use `uv --help` or `uv <command> --help` for inline help
- Add `--verbose` for detailed output during debugging

## Re-scanning

To update this plugin after a CLI version change, run the `/scan-cli` command
or manually execute the crawler and generator.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/nsalvacao) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
