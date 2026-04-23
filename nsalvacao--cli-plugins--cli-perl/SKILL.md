---
name: cli-perl
description: >- Use when this capability is needed.
metadata:
  author: nsalvacao
---

# perl CLI Reference

Compact command reference for **perl** v5.38.2.

- **0** total commands
- **0** command flags + **28** global flags
- **0** extracted usage examples
- Max nesting depth: 0

## When to Use

- Constructing or validating `perl` commands
- Looking up flags/options fast
- Troubleshooting failed invocations

## Top-Level Commands

Command format examples: 

### Global Flags

| Flag | Short | Type | Description |
| --- | --- | --- | --- |
| `-0` | `-0` | string | specify record separator (\0, if no argument) |
| `-C` | `-C` | string | enables the listed Unicode features |
| `-D` | `-D` | string | set debugging flags (argument is a bit mask or alphabets) |
| `-E` | `-E` | string | like -e, but enables all optional features |
| `-F` | `-F` | string | split() pattern for -a switch (//'s are optional) |
| `-Idirectory` | `-Idirectory` | bool | specify @INC/#include directory (several -I's allowed) |
| `-S` | `-S` | bool | look for programfile using PATH environment variable |
| `-T` | `-T` | bool | enable tainting checks |
| `-U` | `-U` | bool | allow unsafe operations |
| `-V[:configvar]` | `-V[:configvar]` | string | print configuration summary (or a single Config.pm variable) |
| `-W` | `-W` | bool | enable all warnings |
| `-X` | `-X` | bool | disable all warnings |
| `-a` | `-a` | bool | autosplit mode with -n or -p (splits $_ into @F) |
| `-c` | `-c` | bool | check syntax only (runs BEGIN and CHECK blocks) |
| `-d[t][:MOD]` | `-d[t][:MOD]` | string | run program under debugger or module Devel::MOD |
| `-e` | `-e` | string | one line of program (several -e's allowed, omit programfile) |
| `-f` | `-f` | bool | don't do $sitelib/sitecustomize.pl at startup |
| `-g` | `-g` | bool | read all input in one go (slurp), rather than line-by-line (alias for -0777) |
| `-i` | `-i` | string | edit <> files in place (makes backup if extension supplied) |
| `-l` | `-l` | string | enable line ending processing, specifies line terminator |
| `-n` | `-n` | bool | assume "while (<>) { ... }" loop around program |
| `-p` | `-p` | bool | assume loop like -n but print line also, like sed |
| `-s` | `-s` | bool | enable rudimentary parsing for switches after programfile |
| `-t` | `-t` | bool | enable tainting warnings |
| `-u` | `-u` | bool | dump core after parsing program |
| `-v` | `-v` | bool | print version, patchlevel and license |
| `-w` | `-w` | bool | enable many useful warnings |
| `-x` | `-x` | string | ignore text before #!perl line (optionally cd to directory) |

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
