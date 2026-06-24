---
name: cli-grep
description: >- Use when this capability is needed.
metadata:
  author: nsalvacao
---

# grep CLI Reference

Compact command reference for **grep** v.

- **0** total commands
- **0** command flags + **49** global flags
- **0** extracted usage examples
- Max nesting depth: 0

## When to Use

- Constructing or validating `grep` commands
- Looking up flags/options fast
- Troubleshooting failed invocations

## Top-Level Commands

Command format examples: 

### Global Flags

| Flag | Short | Type | Description |
| --- | --- | --- | --- |
| `--after-context` | `-A` | string | print NUM lines of trailing context |
| `--basic-regexp` | `-G` | bool | PATTERNS are basic regular expressions |
| `--before-context` | `-B` | string | print NUM lines of leading context |
| `--binary` | `-U` | bool | do not strip CR characters at EOL (MSDOS/Windows) |
| `--binary-files` | `` | string | assume that binary files are TYPE; |
| `--byte-offset` | `-b` | bool | print the byte offset with output lines |
| `--color` | `` | string |  |
| `--colour` | `` | string | use markers to highlight the matching strings; |
| `--context` | `-C` | string | print NUM lines of output context |
| `--count` | `-c` | bool | print only a count of selected lines per FILE |
| `--dereference-recursive` | `-R` | bool | likewise, but follow all symlinks |
| `--devices` | `-D` | string | how to handle devices, FIFOs and sockets; |
| `--directories` | `-d` | string | how to handle directories; |
| `--exclude` | `` | string | skip files that match GLOB |
| `--exclude-dir` | `` | string | skip directories that match GLOB |
| `--exclude-from` | `` | string | skip files that match any file pattern from FILE |
| `--extended-regexp` | `-E` | bool | PATTERNS are extended regular expressions |
| `--file` | `-f` | string | take PATTERNS from FILE |
| `--files-with-matches` | `-l` | bool | print only names of FILEs with selected lines |
| `--files-without-match` | `-L` | bool | print only names of FILEs with no selected lines |
| `--fixed-strings` | `-F` | bool | PATTERNS are strings |
| `--group-separator` | `` | string | print SEP on line between matches with context |
| `--help` | `` | bool | display this help text and exit |
| `--ignore-case` | `-i` | bool | ignore case distinctions in patterns and data |
| `--include` | `` | string | search only files that match GLOB (a file pattern) |
| `--initial-tab` | `-T` | bool | make tabs line up (if needed) |
| `--invert-match` | `-v` | bool | select non-matching lines |
| `--label` | `` | string | use LABEL as the standard input file name prefix |
| `--line-buffered` | `` | bool | flush output on every line |
| `--line-number` | `-n` | bool | print line number with output lines |
| `--line-regexp` | `-x` | bool | match only whole lines |
| `--max-count` | `-m` | string | stop after NUM selected lines |
| `--no-filename` | `-h` | bool | suppress the file name prefix on output |
| `--no-group-separator` | `` | bool | do not print separator for matches with context |
| `--no-ignore-case` | `` | bool | do not ignore case distinctions (default) |
| `--no-messages` | `-s` | bool | suppress error messages |
| `--null` | `-Z` | bool | print 0 byte after FILE name |
| `--null-data` | `-z` | bool | a data line ends in 0 byte, not newline |
| `--only-matching` | `-o` | bool | show only nonempty parts of lines that match |
| `--perl-regexp` | `-P` | bool | PATTERNS are Perl regular expressions |
| `--quiet` | `-q` | bool | suppress all normal output |
| `--recursive` | `-r` | bool | like --directories=recurse |
| `--regexp` | `-e` | string | use PATTERNS for matching |
| `--text` | `-a` | bool | equivalent to --binary-files=text |
| `--version` | `-V` | bool | display version information and exit |
| `--with-filename` | `-H` | bool | print file name with output lines |
| `--word-regexp` | `-w` | bool | match only whole words |
| `-I` | `-I` | bool | equivalent to --binary-files=without-match |
| `-NUM` | `-NUM` | bool | same as --context=NUM |

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
