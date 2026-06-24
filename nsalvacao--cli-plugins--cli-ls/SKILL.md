---
name: cli-ls
description: >- Use when this capability is needed.
metadata:
  author: nsalvacao
---

# ls CLI Reference

Compact command reference for **ls** vunknown.

- **0** total commands
- **0** command flags + **59** global flags
- **0** extracted usage examples
- Max nesting depth: 0

## When to Use

- Constructing or validating `ls` commands
- Looking up flags/options fast
- Troubleshooting failed invocations

## Top-Level Commands

Command format examples: `ls --help`, `ls --version`

### Global Flags

| Flag | Short | Type | Description |
| --- | --- | --- | --- |
| `--all` | `-a` | bool | do not ignore entries starting with . |
| `--almost-all` | `-A` | bool | do not list implied . and .. |
| `--author` | `` | bool | with -l, print the author of each file |
| `--block-size` | `` | string | with -l, scale sizes by SIZE when printing them; |
| `--classify` | `-F` | string | append indicator (one of */=>@|) to entries WHEN |
| `--color` | `` | string | color the output WHEN; more info below |
| `--context` | `-Z` | bool | print any security context of each file |
| `--dereference` | `-L` | bool | when showing file information for a symbolic |
| `--dereference-command-line` | `-H` | bool | follow symbolic links listed on the command line |
| `--dereference-command-line-symlink-to-dir` | `` | bool | follow each command line symbolic link that points to a directory |
| `--directory` | `-d` | bool | list directories themselves, not their contents |
| `--dired` | `-D` | bool | generate output designed for Emacs' dired mode |
| `--escape` | `-b` | bool | print C-style escapes for nongraphic characters |
| `--file-type` | `` | bool | likewise, except do not append '*' |
| `--format` | `` | string | across -x, commas -m, horizontal -x, long -l, |
| `--full-time` | `` | bool | like -l --time-style=full-iso |
| `--group-directories-first` | `` | bool | group directories before files; can be augmented with a --sort option, but any use of --sort=none (-U) disables grouping |
| `--help` | `` | bool | display this help and exit |
| `--hide` | `` | string | do not list implied entries matching shell PATTERN |
| `--hide-control-chars` | `-q` | bool | print ? instead of nongraphic characters |
| `--human-readable` | `-h` | bool | with -l and -s, print sizes like 1K 234M 2G etc. |
| `--hyperlink` | `` | string | hyperlink file names WHEN |
| `--ignore` | `-I` | string | do not list implied entries matching shell PATTERN |
| `--ignore-backups` | `-B` | bool | do not list implied entries ending with ~ |
| `--indicator-style` | `-p` | string | append / indicator to directories |
| `--inode` | `-i` | bool | print the index number of each file |
| `--kibibytes` | `-k` | bool | default to 1024-byte blocks for file system usage; |
| `--literal` | `-N` | bool | print entry names without quoting |
| `--no-group` | `-G` | bool | in a long listing, don't print group names |
| `--numeric-uid-gid` | `-n` | bool | like -l, but list numeric user and group IDs |
| `--quote-name` | `-Q` | bool | enclose entry names in double quotes |
| `--quoting-style` | `` | string | use quoting style WORD for entry names: |
| `--recursive` | `-R` | bool | list subdirectories recursively |
| `--reverse` | `-r` | bool | reverse order while sorting |
| `--show-control-chars` | `` | bool | show nongraphic characters as-is (the default, |
| `--si` | `` | bool | likewise, but use powers of 1000 not 1024 |
| `--size` | `-s` | bool | print the allocated size of each file, in blocks |
| `--sort` | `` | string | sort by WORD instead of name: none (-U), size (-S), |
| `--tabsize` | `-T` | string | assume tab stops at each COLS instead of 8 |
| `--time` | `` | string | select which timestamp used to display or sort; |
| `--time-style` | `` | string | time/date format with -l; see TIME_STYLE below |
| `--version` | `` | bool | output version information and exit |
| `--width` | `-w` | string | set output width to COLS. 0 means no limit |
| `--zero` | `` | bool | end each output line with NUL, not newline |
| `-1` | `-1` | bool | list one file per line |
| `-C` | `-C` | bool | list entries by columns |
| `-S` | `-S` | bool | sort by file size, largest first |
| `-U` | `-U` | bool | do not sort; list entries in directory order |
| `-X` | `-X` | bool | sort alphabetically by entry extension |
| `-c` | `-c` | bool | with -lt: sort by, and show, ctime (time of last |
| `-f` | `-f` | bool | list all entries in directory order |
| `-g` | `-g` | bool | like -l, but do not list owner |
| `-l` | `-l` | bool | use a long listing format |
| `-m` | `-m` | bool | fill width with a comma separated list of entries |
| `-o` | `-o` | bool | like -l, but do not list group information |
| `-t` | `-t` | bool | sort by time, newest first; see --time |
| `-u` | `-u` | bool | with -lt: sort by, and show, access time; |
| `-v` | `-v` | bool | natural sort of (version) numbers within text |
| `-x` | `-x` | bool | list entries by lines instead of by columns |

## Common Usage Patterns (Compact)

```bash
ls --help
```
Display built-in help and global options.

```bash
ls --version
```
Print CLI version information.

## Detailed References

- Full command tree: `references/commands.md`
- Full examples catalog: `references/examples.md`

## Re-Scanning

After a CLI update, run `/scan-cli` or execute crawler + generator again.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/nsalvacao) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
