---
name: cli-jq
description: >- Use when this capability is needed.
metadata:
  author: nsalvacao
---

# jq CLI Reference

Compact command reference for **jq** v.

- **0** total commands
- **0** command flags + **24** global flags
- **0** extracted usage examples
- Max nesting depth: 0

## When to Use

- Constructing or validating `jq` commands
- Looking up flags/options fast
- Troubleshooting failed invocations

## Top-Level Commands

Command format examples: 

### Global Flags

| Flag | Short | Type | Description |
| --- | --- | --- | --- |
| `--args` | `` | bool | consume remaining arguments as positional |
| `--ascii-output` | `-a` | string | output strings by only ASCII characters |
| `--build-configuration` | `` | bool | show jq's build configuration; |
| `--color-output` | `-C` | bool | colorize JSON output; |
| `--compact-output` | `-c` | bool | compact instead of pretty-printed output; |
| `--exit-status` | `-e` | bool | set exit status code based on the output; |
| `--from-file` | `-f` | string | load filter from the file; |
| `--help` | `-h` | bool | show the help; |
| `--indent` | `` | string | use n spaces for indentation (max 7 spaces); |
| `--join-output` | `-j` | bool | implies -r and output without newline after |
| `--jsonargs` | `` | bool | consume remaining arguments as positional |
| `--monochrome-output` | `-M` | bool | disable colored output; |
| `--null-input` | `-n` | string | use `null` as the single input value; |
| `--raw-input` | `-R` | string | read each line as string instead of JSON; |
| `--raw-output` | `-r` | string | output strings without escapes and quotes; |
| `--raw-output0` | `` | bool | implies -r and output NUL after each output; |
| `--seq` | `` | bool | parse input/output as application/json-seq; |
| `--slurp` | `-s` | bool | read all inputs into an array and use it as |
| `--sort-keys` | `-S` | bool | sort keys of each object on output; |
| `--stream` | `` | string | parse the input value in streaming fashion; |
| `--stream-errors` | `` | bool | implies --stream and report parse error as |
| `--tab` | `` | bool | use tabs for indentation; |
| `--unbuffered` | `` | bool | flush output stream after each output; |
| `--version` | `-V` | bool | show the version; |

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
