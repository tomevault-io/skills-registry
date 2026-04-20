---
name: fetching-npm-source
description: Fetches source code for npm packages and GitHub repositories to provide AI agents with implementation details beyond types. Use when you need to understand how a package works internally or reference its actual implementation. Use when this capability is needed.
metadata:
  author: oxfrancesco
---

# Fetching NPM Source Code

Uses the `opensrc` CLI tool to fetch source code for npm packages and GitHub repositories, enabling deeper context for analyzing implementations.

## Capabilities

- **Fetch npm packages**: Download source code for any npm package at the installed version
- **Fetch GitHub repositories**: Clone source code directly from any public GitHub repo
- **Auto-detect versions**: Automatically matches installed package versions from lockfiles
- **List sources**: View all previously fetched packages
- **Remove sources**: Clean up fetched source code
- **Auto-configure**: Optionally updates `.gitignore`, `tsconfig.json`, and `AGENTS.md`

## Installation

The skill uses `opensrc`, which must be installed globally:

```bash
npm install -g opensrc
```

Or use with npx (no installation needed):

```bash
npx opensrc <package>
```

## Common Workflows

### Fetch a Single Package

Get source code for a package at your installed version:

```bash
opensrc zod
```

This stores source in `opensrc/zod/` and auto-detects the version from your lockfile.

### Fetch Specific Version

```bash
opensrc zod@3.22.0
```

### Fetch Multiple Packages

```bash
opensrc react react-dom next
```

### Fetch GitHub Repository

```bash
# Using shorthand
opensrc facebook/react

# Using full GitHub URL
opensrc https://github.com/colinhacks/zod

# Fetch specific branch or tag
opensrc owner/repo@v1.0.0
```

GitHub repos are stored as `opensrc/owner--repo/`.

### List All Fetched Sources

```bash
opensrc list
```

Displays all packages with versions and paths. The data is stored in `opensrc/sources.json`.

### Remove a Source

```bash
# Remove package
opensrc remove zod

# Remove GitHub repo
opensrc remove owner--repo
```

## File Modifications

On first run, `opensrc` asks permission to modify:

- `.gitignore` — adds `opensrc/` to ignore list
- `tsconfig.json` — excludes `opensrc/` from compilation
- `AGENTS.md` — adds section pointing agents to source code

Skip the prompt with:

```bash
opensrc zod --modify      # Allow modifications
opensrc zod --modify=false  # Deny modifications
```

Preferences are saved to `opensrc/settings.json`.

## Output Structure

After fetching, your project contains:

```
opensrc/
├── settings.json       # Your modification preferences
├── sources.json        # Index of fetched packages
└── zod/
    ├── src/
    ├── package.json
    └── ... (all source files)
```

The `sources.json` tracks what's available:

```json
{
  "packages": [
    { "name": "zod", "version": "3.22.0", "path": "opensrc/zod" }
  ]
}
```

## Use Cases

- **Understanding implementation details**: When types alone aren't enough
- **Reviewing package code**: Before relying on a dependency
- **Finding examples**: See how a package uses internal features
- **Debugging**: Trace through actual implementations
- **Learning**: Study well-written open source code

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/oxfrancesco) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
