---
name: hk-setup
description: Set up hk (git hook manager) with pre-commit hooks for any project. Detects project type (Python, JS/TS, Go, Rust, Swift, Shell) and configures appropriate linters/formatters. Use when user wants to add pre-commit hooks, set up hk, configure linting, or mentions git hooks. Use when this capability is needed.
metadata:
  author: kennyg
---

# hk Setup

Configure [hk](https://hk.jdx.dev) git hooks with appropriate linters for any project.

## Prerequisites

```bash
brew install hk
```

Install linters globally (hk runs tools in a subshell):

```bash
# Universal
brew install typos-cli

# Shell
brew install shellcheck shfmt

# Python
brew install ruff

# JS/TS
brew install biome

# Go
brew install golangci-lint

# TOML
brew install taplo
```

## Workflow

1. Detect project type (look for pyproject.toml, package.json, go.mod, etc.)
2. Create `hk.pkl` with appropriate linters
3. Run `hk validate && hk install`
4. Test with `hk check --all`

## Project Detection

| File | Type | Recommended Linters |
|------|------|---------------------|
| `pyproject.toml` | Python | `ruff`, `ruff_format` |
| `package.json` | JS/TS | `biome` (or `eslint` + `prettier`) |
| `go.mod` | Go | `go_fmt`, `go_imports`, `golangci_lint` |
| `Cargo.toml` | Rust | `rustfmt`, `cargo_clippy` |
| `Package.swift` | Swift | `swiftlint` |
| `*.sh` | Shell | `shellcheck`, `shfmt` |
| `Dockerfile` | Docker | `hadolint` |

## hk.pkl Template

```pkl
amends "package://github.com/jdx/hk/releases/download/v1.34.0/hk@1.34.0#/Config.pkl"
import "package://github.com/jdx/hk/releases/download/v1.34.0/hk@1.34.0#/Builtins.pkl"

local linters = new Mapping<String, Step> {
    // Add linters based on project type
}

hooks {
    ["pre-commit"] {
        fix = true
        stash = "git"
        steps = linters
    }
    ["pre-push"] {
        steps = linters
    }
    ["fix"] {
        fix = true
        steps = linters
    }
    ["check"] {
        steps = linters
    }
}
```

## Project Templates

### Python

```pkl
local linters = new Mapping<String, Step> {
    ["ruff"] = Builtins.ruff
    ["ruff-format"] = Builtins.ruff_format
}
```

### JavaScript/TypeScript (biome)

```pkl
local linters = new Mapping<String, Step> {
    ["biome"] = Builtins.biome
}
```

### JavaScript/TypeScript (eslint + prettier)

```pkl
local linters = new Mapping<String, Step> {
    ["eslint"] = Builtins.eslint
    ["prettier"] = Builtins.prettier
}
```

### Go

```pkl
local linters = new Mapping<String, Step> {
    ["gofmt"] = Builtins.go_fmt
    ["goimports"] = Builtins.go_imports
    ["golangci-lint"] = Builtins.golangci_lint
}
```

### Rust

```pkl
local linters = new Mapping<String, Step> {
    ["rustfmt"] = Builtins.rustfmt
    ["clippy"] = Builtins.cargo_clippy
}
```

### Shell

```pkl
local linters = new Mapping<String, Step> {
    ["shellcheck"] = Builtins.shellcheck
    ["shfmt"] = Builtins.shfmt
}
```

## Universal Linters

Add to any project:

```pkl
["typos"] = Builtins.typos                   // Spell checker
["trailing-whitespace"] = Builtins.trailing_whitespace
["check-merge-conflict"] = Builtins.check_merge_conflict
```

## Custom Steps

```pkl
["my-linter"] {
    glob = "**/*.ext"
    check = "my-tool check {{files}}"
    fix = "my-tool fix {{files}}"
}
```

## Commands

```bash
hk validate       # Check config syntax
hk install        # Install git hooks
hk check --all    # Run all checks
hk fix --all      # Auto-fix issues
hk builtins       # List available builtins
```

## Resources

- **references/builtins.md** - Complete builtin linters catalog

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kennyg) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
