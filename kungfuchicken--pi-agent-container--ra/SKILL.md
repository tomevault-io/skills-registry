---
name: ra
description: Run rust-analyzer CLI commands for Rust code analysis - diagnostics, unresolved references, structural search, and analysis stats. Use when analyzing Rust code quality, finding issues, or performing structural code searches. Use when this capability is needed.
metadata:
  author: kungfuchicken
---

# rust-analyzer CLI Skill

You have access to rust-analyzer's powerful CLI for Rust code analysis. Use the Bash tool to run these commands.

## Invocation

The user invokes this skill with `/ra <command> [args]`. Parse `$ARGUMENTS` to determine which operation to perform.

**Arguments received**: $ARGUMENTS

## Commands Reference

### `diag` or `diagnostics` — Project Diagnostics

Run compiler diagnostics on a Rust project.

```bash
rust-analyzer diagnostics <project-path>
```

**Project path**: Find the nearest `Cargo.toml` from the current working directory, or use a path specified in arguments.

**Example output interpretation**: Errors and warnings with file locations and messages.

### `refs` or `unresolved` — Unresolved References

Find all unresolved references in a project (missing imports, undefined names, etc.).

```bash
rust-analyzer unresolved-references <project-path>
```

**Useful for**: Finding missing imports, typos in identifiers, incomplete refactors.

### `stats` — Analysis Statistics

Get analysis statistics for a project.

```bash
rust-analyzer analysis-stats <project-path>
```

**Shows**: Module count, function count, type analysis coverage, macro expansion stats.

### `search <pattern>` — Structural Code Search

Search for structural code patterns using rust-analyzer's pattern matching. Operates on the current working directory.

```bash
rust-analyzer search '<pattern>' 2>&1
```

**Note**: Output shows matching code snippets without file paths. Use `grep` after to locate specific matches if needed.

**Pattern syntax**:
- `$name` matches any single AST node and binds it to `name`
- `$name:expr` matches expressions
- `$name:stmt` matches statements
- `$name:ty` matches types
- `$name:pat` matches patterns
- `$name:item` matches items (functions, structs, etc.)
- `$name:path` matches paths
- `$$name` matches zero or more AST nodes (like regex `*`)

**Examples**:
- `$a.unwrap()` — find all `.unwrap()` calls
- `if let Some($x) = $y { $$ }` — find if-let-some patterns
- `fn $name($args) -> Result<$ok, $err>` — find functions returning Result
- `$receiver.map($f).unwrap_or($default)` — find map+unwrap_or chains

### `ssr <rule>` — Structural Search and Replace

Perform structural search-and-replace transformations.

```bash
rust-analyzer ssr '<pattern> ==>> <replacement>'
```

**Note**: This outputs the transformation but does NOT modify files. Review output and apply manually if desired.

**Examples**:
- `$a.unwrap() ==>> $a?` — preview replacing unwrap with ?
- `Option::Some($x) ==>> Some($x)` — simplify Option::Some
- `$a.map($f).unwrap_or($d) ==>> $a.map_or($d, $f)` — use map_or

### `symbols` — Parse Symbols from Stdin

Parse Rust code from stdin and list symbols.

```bash
echo '<rust-code>' | rust-analyzer symbols
```

Or read a file:

```bash
cat <file.rs> | rust-analyzer symbols
```

## Execution Strategy

1. **Parse the command** from `$ARGUMENTS`:
   - First word is the command (diag, refs, stats, search, ssr, symbols)
   - Remaining words are command-specific arguments

2. **Locate the project**:
   - If a path is provided, use it
   - Otherwise, find nearest Cargo.toml from current working directory
   - For workspace with multiple crates, use the workspace root

3. **Run the command** using Bash tool

4. **Interpret output**:
   - For diagnostics: summarize errors/warnings by severity and file
   - For search/ssr: show matches with context
   - For stats: highlight interesting metrics

5. **Suggest follow-ups** when relevant:
   - After diagnostics: offer to help fix specific issues
   - After search: offer to apply transformations or refactor

## Finding the Project Root

Use this to locate the Rust project:

```bash
# Find nearest Cargo.toml
cargo locate-project --message-format=plain 2>/dev/null | xargs dirname
```

Or for workspace root:

```bash
cargo metadata --no-deps --format-version=1 2>/dev/null | jq -r '.workspace_root'
```

## Error Handling

- If rust-analyzer isn't installed: suggest `rustup component add rust-analyzer`
- If no Cargo.toml found: ask user to specify project path
- If pattern syntax is wrong: explain pattern syntax with examples

## Examples of Full Invocations

User: `/ra diag`
→ Run diagnostics on current project

User: `/ra refs acme/myapp`
→ Find unresolved references in myapp project

User: `/ra search '$a.unwrap()'`
→ Find all .unwrap() calls in current project

User: `/ra ssr '$a.unwrap() ==>> $a?'`
→ Preview replacing unwrap with ? operator

User: `/ra stats`
→ Show analysis statistics for current project

---
> Source: [kungfuchicken/pi-agent-container](https://github.com/kungfuchicken/pi-agent-container) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-23 -->
