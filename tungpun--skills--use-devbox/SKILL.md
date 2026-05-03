---
name: use-devbox
description: Search and install packages using devbox when a tool or command is missing or unavailable in the local environment Use when this capability is needed.
metadata:
  author: tungpun
---

# Use Devbox

Use devbox to manage packages locally for this project without affecting the global system environment.

## When to Use This Skill

- A command or tool is not found (e.g., "command not found", "jq: not found")
- User asks to install a package or tool
- A build or script fails due to missing dependencies
- User wants to search for available packages

## When NOT to Use

- The tool is already installed (check with `devbox list` first)
- The package is a language-specific dependency (use npm, pip, go get, etc. instead)
- User explicitly wants global installation

## Instructions

### Step 1: Check Current Packages

First, check what's already installed:
```bash
devbox list
```

### Step 2: Search for Packages

Search for available packages in the Nix repository:
```bash
devbox search <package-name>
```

This returns available versions. Present the top results to the user.

### Step 3: Confirm Before Installing

**IMPORTANT:** Always ask the user for confirmation before installing. Show:
- The exact package name and version to install
- Brief description of what it does (if known)

Example: "I found `jq@latest` (JSON processor). Should I install it with devbox?"

### Step 4: Install the Package

After user confirms, install with:
```bash
devbox add <package>@<version>
```

Use `@latest` for the most recent version unless user specifies otherwise.

### Step 5: Verify Installation

Confirm it works:
```bash
devbox run <package> --version
```

Or enter the devbox shell to test:
```bash
devbox shell
```

## Common Commands Reference

| Command | Purpose |
|---------|---------|
| `devbox list` | Show installed packages |
| `devbox search <pkg>` | Find available packages |
| `devbox add <pkg>@<ver>` | Install a package |
| `devbox rm <pkg>` | Remove a package |
| `devbox run <cmd>` | Run command in devbox env |
| `devbox shell` | Enter devbox shell |

## Notes

- All packages are installed locally in `devbox.json`
- Does not require sudo or affect system packages
- Packages come from the Nix package repository

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tungpun) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
