---
name: git-workflow
description: Dotfiles-specific git workflow manager. Use when committing, branching, managing symlink history, rebasing, or handling the multi-machine/multi-platform nature of this repo. Use when this capability is needed.
metadata:
  author: icub3d
---

# Git Workflow

## Overview
This skill manages git operations specific to the dotfiles repo at `~/dev/dotfiles`. The repo is unusual: it contains symlinkable config files, Nushell install scripts, platform-specific branches, and machine-specific overlays. Standard git workflows need adjustment to avoid committing secrets, large binaries, or machine-local state.

## Repo Structure Context
- `dotfiles/` — files that get symlinked into `~` via `install.nu`
- `helpers/` — Nushell scripts and systemd units deployed by `install.nu`
- `nushell/` — shell config; `local.nu` is machine-local and NOT tracked
- `packages/pacman/` — package lists per feature group
- `vms/` — libvirt XML; may contain machine-specific PCI addresses
- `agents/skills/` — Claude Code skills

## Core Capabilities

### 1. Safe Staging
Files to **never** commit:
- `nushell/local.nu` — machine-local env vars and secrets
- `nushell/history.sqlite3*` — shell history
- `helpers/gpg-agent.conf` if it contains machine-specific socket paths
- Any `*.key`, `*.pem`, `*.env` files

Always check `git diff --staged` before committing to catch accidental secret inclusion.

### 2. Commit Conventions
- Use imperative present tense: "add", "fix", "update", "remove" — not "added" or "fixes".
- Scope commits by subsystem when practical: `niri: add scratchpad keybinding`, `packages: add gaming group`.
- Keep the subject under 72 characters; body is optional for single-file changes.

### 3. Platform Branching
The repo targets multiple platforms. Platform-specific config lives in:
- `nushell/linux.nu`, `nushell/macos.nu`, `nushell/windows.nu`
- `install.nu` dispatches by `$nu.os-info.name`

When backporting a change to another platform, prefer editing the platform file over conditionals in `config.nu`.

### 4. Symlink Hygiene
`install.nu` creates symlinks from `~/<path>` → `~/dev/dotfiles/dotfiles/<path>`. After adding a new file under `dotfiles/`:
1. Add the symlink entry to `install.nu` (see `dotfiles-guardian` skill).
2. Stage both the new file and the `install.nu` change together.
3. Verify the symlink resolves: `ls -la ~/<target>`.

### 5. Keeping History Clean
- Squash WIP commits before pushing: `git rebase -i origin/main`.
- Do not force-push `main` — it is the primary branch used across machines.
- If a secret was accidentally committed, use `git filter-repo` (not `filter-branch`) to excise it, then rotate the secret.

### 6. Updating Across Machines
On a new machine after pulling:
```nushell
nu install.nu          # re-run to pick up new symlinks
systemctl --user daemon-reload  # if systemd units changed
```

## Examples
- "Commit the new niri keybinding I just added."
- "What files have I changed that aren't staged yet?"
- "I accidentally staged local.nu — unstage it without losing my changes."
- "Squash my last three commits before pushing."

---
> Source: [icub3d/dotfiles](https://github.com/icub3d/dotfiles) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
