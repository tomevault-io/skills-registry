---
name: cli-langchain
description: >- Use when this capability is needed.
metadata:
  author: nsalvacao
---

# langchain CLI Reference

Expert command reference for **langchain** v0.0.37.

- **14** commands (3 with subcommands)
- **40** command flags + **2** global flags
- **0** usage examples
- Max nesting depth: 1

## When to Use

This skill applies when:
- Constructing or validating `langchain` commands
- Looking up flags, options, or subcommands
- Troubleshooting `langchain` invocations or errors
- Needing correct syntax for `langchain` operations

## Prerequisites

Install langchain:
```bash
npm install -g langchain@latest
```

## Quick Reference

| Command | Description |
| --- | --- |
| `langchain app` | Manage LangChain apps. |
| `langchain integration` | Develop integration packages for LangChain. |
| `langchain migrate` | Migrate langchain to the most recent version. |
| `langchain serve` | Start the LangServe app, whether it's a template or an app. |
| `langchain template` | Develop installable templates. |

### Global Flags

| Flag | Short | Type | Description |
| --- | --- | --- | --- |
| `--help` | `` | bool | Show this message and exit. |
| `--version` | `-v` | bool | Print the current CLI version. |

## Command Overview


### Command Groups

`app`, `integration`, `template`

### Commands

`migrate`, `serve`

## Common Usage Patterns


## Detailed References

For complete command documentation including all flags and subcommands:
- **Full command tree:** see `references/commands.md`
- **All usage examples:** see `references/examples.md`

## Troubleshooting

- Use `langchain --help` or `langchain <command> --help` for inline help
- Add `--verbose` for detailed output during debugging

## Re-scanning

To update this plugin after a CLI version change, run the `/scan-cli` command
or manually execute the crawler and generator.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/nsalvacao) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
