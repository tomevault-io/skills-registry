---
name: cli-pip
description: >- Use when this capability is needed.
metadata:
  author: nsalvacao
---

# pip CLI Reference

Expert command reference for **pip** v24.0.

- **17** commands (0 with subcommands)
- **575** command flags + **25** global flags
- **0** usage examples
- Max nesting depth: 0

## When to Use

This skill applies when:
- Constructing or validating `pip` commands
- Looking up flags, options, or subcommands
- Troubleshooting `pip` invocations or errors
- Needing correct syntax for `pip` operations

## Prerequisites

Ensure `pip` is installed and available on PATH.

## Quick Reference

| Command | Description |
| --- | --- |
| `pip cache` | Inspect and manage pip's wheel cache. |
| `pip check` | Verify installed packages have compatible dependencies. |
| `pip completion` | A helper command to be used for command completion. |
| `pip config` | Manage local and global configuration. |
| `pip debug` | Display debug information. |
| `pip download` | Download packages from: |
| `pip freeze` | Output installed packages in requirements format. |
| `pip hash` | Compute a hash of a local package archive. |
| `pip help` | Show help for commands |
| `pip index` | Inspect information available from package indexes. |
| `pip inspect` | Inspect the content of a Python environment and produce a report in JSON format. |
| `pip install` | Install packages from: |
| `pip list` | List installed packages, including editables. |
| `pip search` | Search for PyPI packages whose name or summary contains <query>. |
| `pip show` | Show information about one or more installed packages. |
| `pip uninstall` | Uninstall packages. |
| `pip wheel` | Build Wheel archives for your requirements and dependencies. |

### Global Flags

| Flag | Short | Type | Description |
| --- | --- | --- | --- |
| `--cache-dir` | `` | string | Store the cache data in <dir>. |
| `--cert` | `` | string | Path to PEM-encoded CA certificate bundle. If provided, overrides the default. See 'SSL |
| `--client-cert` | `` | string | Path to SSL client certificate, a single file containing the private key and the |
| `--debug` | `` | bool | Let unhandled exceptions propagate outside the main subroutine, instead of logging them |
| `--disable-pip-version-check` | `` | bool | Don't periodically check PyPI to determine whether a new version of pip is available for download. Implied with --no-index. |
| `--exists-action` | `` | string | Default action when a path already exists: (s)witch, (i)gnore, (w)ipe, (b)ackup, |
| `--help` | `-h` | bool | Show help. |
| `--isolated` | `` | bool | Run pip in an isolated mode, ignoring environment variables and user configuration. |
| `--keyring-provider` | `` | string | Enable the credential lookup via the keyring library if user input is allowed. Specify which mechanism to use [disabled, import, subprocess]. (default: disabled) (default: disabled) |
| `--log` | `` | string | Path to a verbose appending log. |
| `--no-cache-dir` | `` | bool | Disable the cache. |
| `--no-color` | `` | bool | Suppress colored output. |
| `--no-input` | `` | bool | Disable prompting for input. |
| `--no-python-version-warning` | `` | bool | Silence deprecation warnings for upcoming unsupported Pythons. |
| `--proxy` | `` | string | Specify a proxy in the form scheme://[user:passwd@]proxy.server:port. |
| `--python` | `` | string | Run pip with the specified Python interpreter. |
| `--quiet` | `-q` | bool | Give less output. Option is additive, and can be used up to 3 times (corresponding to |
| `--require-virtualenv` | `` | bool | Allow pip to only run in a virtual environment; exit with an error otherwise. |
| `--retries` | `` | string | Maximum number of retries each connection should attempt (default 5 times). |
| `--timeout` | `` | string | Set the socket timeout (default 15 seconds). |
| `--trusted-host` | `` | string | Mark this host or host:port pair as trusted, even though it does not have valid or any |
| `--use-deprecated` | `` | string | Enable deprecated functionality, that will be removed in the future. |
| `--use-feature` | `` | string | Enable new functionality, that may be backward incompatible. |
| `--verbose` | `-v` | bool | Give more output. Option is additive, and can be used up to 3 times. |
| `--version` | `-V` | bool | Show version and exit. |

## Command Overview


### Commands

`cache`, `check`, `completion`, `config`, `debug`, `download`, `freeze`, `hash`, `help`, `index`, `inspect`, `install`, `list`, `search`, `show`, `uninstall`, `wheel`

## Common Usage Patterns


## Detailed References

For complete command documentation including all flags and subcommands:
- **Full command tree:** see `references/commands.md`
- **All usage examples:** see `references/examples.md`

## Troubleshooting

- Use `pip --help` or `pip <command> --help` for inline help
- Add `--verbose` for detailed output during debugging

## Re-scanning

To update this plugin after a CLI version change, run the `/scan-cli` command
or manually execute the crawler and generator.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/nsalvacao) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
