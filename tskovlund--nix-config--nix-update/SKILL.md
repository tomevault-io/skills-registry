---
name: nix-update
description: > Use when this capability is needed.
metadata:
  author: tskovlund
---

# Nix-config update

Pull remote changes and update flake inputs. Full workflow: fetch → pull → update → check → switch → commit.

## Pre-flight

Check current state before making changes:

```sh
git status --short
git log --oneline origin/main..HEAD
```

If there are uncommitted changes or unpushed commits, warn before proceeding — updating on a dirty tree risks conflicts.

## Step 1: Pull remote changes

```sh
git fetch --quiet
git pull --rebase
```

If there are conflicts, stop and resolve before continuing.

## Step 2: Update flake inputs

```sh
nix flake update
```

This updates all inputs (nixpkgs, home-manager, nix-darwin, agenix, etc.) to their latest versions.

To update a single input:

```sh
nix flake lock --update-input <name>
```

## Step 3: Validate

```sh
make check
```

If check fails, the update introduced a breaking change. Common causes:

- home-manager option renamed or removed
- nix-darwin API change
- nixpkgs package removed or renamed

Use `/nix-debug` to investigate failures. If an input broke, see "Follows override" in CLAUDE.md for pinning strategies.

## Step 4: Deploy

```sh
make switch
```

For non-visual changes, verify the change took effect. For visual changes, ask Thomas to verify.

If deploying with local personal flake changes:

```sh
make switch PERSONAL_INPUT=path:$HOME/repos/nix-config-personal
```

## Step 5: Commit

```sh
nix develop --command git commit -a -m "chore(deps): update flake inputs"
```

If remote changes were also pulled, the lock update should be a separate commit from any other work.

## Step 6: Push

```sh
nix develop --command git push
```

## Also update nix-config-personal

If nix-config-personal also has stale inputs:

```sh
cd ~/repos/nix-config-personal
git pull --rebase
nix flake update
nix develop --command git commit -a -m "chore(deps): update flake inputs"
nix develop --command git push
```

Then re-deploy nix-config with the updated personal flake:

```sh
cd ~/repos/nix-config
make switch REFRESH=1
```

## Frequency

Flake inputs should be updated roughly every 2 weeks to stay current with security patches and package updates. The `repo-sync` skill flags when `flake.lock` is older than 14 days.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tskovlund) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
