---
name: cli-ruff
description: >- Use when this capability is needed.
metadata:
  author: nsalvacao
---

# ruff CLI Reference

Expert command reference for **ruff** v0.14.13.

- **12** commands (1 with subcommands)
- **120** command flags + **7** global flags
- **0** usage examples
- Max nesting depth: 1

## When to Use

This skill applies when:
- Constructing or validating `ruff` commands
- Looking up flags, options, or subcommands
- Troubleshooting `ruff` invocations or errors
- Needing correct syntax for `ruff` operations

## Prerequisites

Ensure `ruff` is installed and available on PATH.

## Quick Reference

| Command | Description |
| --- | --- |
| `ruff analyze` | Run analysis over Python source code |
| `ruff check` | Run Ruff on the given files or directories |
| `ruff clean` | Clear any caches in the current directory and any subdirectories |
| `ruff config` | List or describe the available configuration options |
| `ruff format` | Run the Ruff formatter on the given files or directories |
| `ruff help` | error: unrecognized subcommand '--help' |
| `ruff linter` | List all supported upstream linters |
| `ruff rule` | Explain a rule (or all rules) |
| `ruff server` | Run the language server |
| `ruff version` | Display Ruff's version |

### Global Flags

| Flag | Short | Type | Description |
| --- | --- | --- | --- |
| `--config` | `` | string | Either a path to a TOML configuration file (`pyproject.toml` or `ruff.toml`), or a TOML |
| `--help` | `-h` | bool | Print help |
| `--isolated` | `` | string | Ignore all configuration files |
| `--quiet` | `-q` | bool | Print diagnostics, but nothing else |
| `--silent` | `-s` | bool | Disable all logging (but still exit with status code "1" upon detecting diagnostics) |
| `--verbose` | `-v` | bool | Enable verbose logging |
| `--version` | `-V` | bool | Print version |

## Command Overview


### Command Groups

`analyze`

### Commands

`check`, `clean`, `config`, `format`, `help`, `linter`, `rule`, `server`, `version`

## Common Usage Patterns


## Detailed References

For complete command documentation including all flags and subcommands:
- **Full command tree:** see `references/commands.md`
- **All usage examples:** see `references/examples.md`

## Troubleshooting

- Use `ruff --help` or `ruff <command> --help` for inline help
- Add `--verbose` for detailed output during debugging

## Re-scanning

To update this plugin after a CLI version change, run the `/scan-cli` command
or manually execute the crawler and generator.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/nsalvacao) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
