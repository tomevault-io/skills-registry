---
name: update-completions
description: > Use when this capability is needed.
metadata:
  author: scaryrawr
---

# Update Fish Shell Completions for Claude Code

## Overview

This skill updates `completions/claude.fish` to stay in sync with the latest
Claude Code CLI flags, subcommands, and options.

## Steps

### 1. Gather current CLI help output

Run each of these commands and capture the output:

```bash
claude --help
claude mcp --help
claude mcp add --help
claude mcp remove --help
claude mcp add-json --help
claude mcp add-from-claude-desktop --help
claude mcp serve --help
claude mcp list --help
claude mcp get --help
claude mcp reset-project-choices --help
claude plugin --help
claude plugin validate --help
claude plugin marketplace --help
claude plugin marketplace add --help
claude plugin marketplace list --help
claude plugin marketplace remove --help
claude plugin marketplace update --help
claude plugin install --help
claude plugin uninstall --help
claude plugin enable --help
claude plugin disable --help
claude plugin update --help
claude install --help
claude update --help
claude doctor --help
claude setup-token --help
```

### 2. Compare against existing completions

Read the current `completions/claude.fish` file and compare it against the help
output collected in step 1. Look for:

- **New flags or options** not yet in the completions file.
- **Removed flags or options** that should be deleted.
- **Changed descriptions** that should be updated.
- **New subcommands** that need completion entries.
- **Removed subcommands** that should be deleted.
- **New or changed allowed values** for flags that accept specific values
  (e.g., `--output-format`, `--permission-mode`, `--transport`).

### 3. Update the completions file

Edit `completions/claude.fish` following these patterns:

#### Top-level flags

```fish
complete -c claude -s X -l long-name -d "Description from help"
# For flags with specific allowed values:
complete -c claude -l flag-name -xa "val1 val2 val3" -d "Description"
```

#### Subcommands

```fish
complete -c claude -n __fish_use_subcommand -xa "subcommand" -d "Description"
```

#### Nested subcommands

```fish
complete -c claude -n "__fish_seen_subcommand_from parent" -xa "child" -d "Description"
```

#### Subcommand-specific flags

```fish
complete -c claude -n "__fish_seen_subcommand_from subcmd" -l flag -d "Description"
```

#### Dynamic completions (helper functions)

The file contains `__claude_*` helper functions that call `claude` CLI with
`--json` at tab-completion time. These provide live argument suggestions for
plugin and marketplace subcommands. When updating:

- **Do not remove or rename** the helper functions unless the underlying CLI
  commands change.
- If a new subcommand accepts a plugin or marketplace name as a positional
  argument, add a dynamic completion line using the appropriate helper function
  (e.g., `-xa "(__claude_installed_plugins)"`).
- Helper functions parse JSON using `string match -r` / `string replace` — no
  `jq` dependency.

- Keep `complete -c claude -f` as the first line (disables file completions).
- Group related completions together with comments.
- Use `-xa` for exclusive completion values.
- Use `-s` for short flags and `-l` for long flags.
- Descriptions (`-d`) should match the CLI help text.

### 4. Test the changes

```fish
source completions/claude.fish
complete -C "claude "
complete -C "claude mcp "
complete -C "claude --output-format "
complete -C "claude plugin install "
complete -C "claude plugin marketplace remove "
```

Verify that new completions appear, dynamic completions return expected values,
and no errors are raised.

### 5. Summarize changes

List what was added, removed, or updated so the user can review before
committing.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/scaryrawr) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
