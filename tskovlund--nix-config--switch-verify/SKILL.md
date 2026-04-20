---
name: switch-verify
description: > Use when this capability is needed.
metadata:
  author: tskovlund
---

# Deploy and verify

## Standard workflow

### 1. Pre-flight validation

```sh
make check
```

This runs `nix flake check --all-systems` — catches eval errors, type mismatches, and build failures across all platforms before deploying.

### 2. Deploy

```sh
make switch
```

Auto-detects the current platform (macOS / Linux / NixOS-WSL) and applies the config.

### 3. Verify

**Non-visual changes** (packages, env vars, aliases, services):

- Verify the change took effect (e.g., `which <tool>`, `echo $VAR`, test the alias)
- If everything works, commit

**Visual changes** (shell prompts, themes, TUI config, statusline, editor appearance):

- Describe what changed and what to look for
- Ask Thomas to verify the result visually before committing
- `make check` passing does NOT mean visual changes look correct

## Variants

### Testing local personal flake changes

```sh
make switch PERSONAL_INPUT=path:$HOME/repos/nix-config-personal
```

Uses the local checkout of nix-config-personal instead of the remote input.

### Testing with machine-local config

```sh
make switch IMPURE=1
```

Includes `~/.config/nix-config/local.nix` if it exists.

### Bypassing input cache

```sh
make switch REFRESH=1
```

Useful after pushing to the personal flake remote — forces Nix to re-fetch.

### Combining flags

```sh
make switch PERSONAL_INPUT=path:$HOME/repos/nix-config-personal IMPURE=1
```

## Troubleshooting

If `make check` or `make switch` fails, use `/nix-debug` for debugging strategies (eval errors, build failures, flake issues).

## Rollback

Nix config is declarative — to roll back, just fix the issue and re-switch. No manual rollback commands needed.

## Pre-commit note

Git commands that trigger hooks require dev shell tools. If not already in the dev shell:

```sh
nix develop --command git commit -m "message"
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tskovlund) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
