---
name: cli-make
description: >- Use when this capability is needed.
metadata:
  author: nsalvacao
---

# make CLI Reference

Compact command reference for **make** v.

- **0** total commands
- **0** command flags + **18** global flags
- **0** extracted usage examples
- Max nesting depth: 0

## When to Use

- Constructing or validating `make` commands
- Looking up flags/options fast
- Troubleshooting failed invocations

## Top-Level Commands

Command format examples: 

### Global Flags

| Flag | Short | Type | Description |
| --- | --- | --- | --- |
| `--always-make` | `-B` | bool | Unconditionally make all targets. |
| `--check-symlink-times` | `-L` | bool | Use the latest mtime between symlinks and target. |
| `--environment-overrides` | `-e` | string | Environment variables override makefiles. |
| `--help` | `-h` | bool | Print this message and exit. |
| `--ignore-errors` | `-i` | bool | Ignore errors from recipes. |
| `--keep-going` | `-k` | bool | Keep going when some targets can't be made. |
| `--no-builtin-rules` | `-r` | bool | Disable the built-in implicit rules. |
| `--no-builtin-variables` | `-R` | bool | Disable the built-in variable settings. |
| `--no-print-directory` | `` | bool | Turn off -w, even if it was turned on implicitly. |
| `--no-silent` | `` | bool | Echo recipes (disable --silent mode). |
| `--print-data-base` | `-p` | bool | Print make's internal database. |
| `--print-directory` | `-w` | string | Print the current directory. |
| `--question` | `-q` | bool | Run no recipe; exit status says if up to date. |
| `--touch` | `-t` | bool | Touch targets instead of remaking them. |
| `--trace` | `` | bool | Print tracing information. |
| `--version` | `-v` | string | Print the version number of make and exit. |
| `--warn-undefined-variables` | `` | bool | Warn when an undefined variable is referenced. |
| `-d` | `-d` | bool | Print lots of debugging information. |

## Common Usage Patterns (Compact)

_No examples extracted._
## Detailed References

- Full command tree: `references/commands.md`
- Full examples catalog: `references/examples.md`

## Re-Scanning

After a CLI update, run `/scan-cli` or execute crawler + generator again.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/nsalvacao) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
