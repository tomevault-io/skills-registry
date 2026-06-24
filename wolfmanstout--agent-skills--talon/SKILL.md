---
name: talon
description: Use when working on Talon voice command projects — writing .talon files, .talon-list files, or Talon Python modules, AND when installing or deploying Talon scripts to the user's Talon directory. Provides file syntax, voice command rules, context matching, list definitions, Python API tips, local testing, and sandbox testing via talonbox.
metadata:
  author: wolfmanstout
---

# Talon Voice Command Development

## Reference Documentation

For detailed syntax documentation, see bundled references:
- `references/talon-files.md` — .talon file syntax, context headers, commands, actions, captures, settings, tags
- `references/talon-lists.md` — .talon-list file format, overrides, named vs simple lists
- `references/misc-tips.md` — REPL and logging usage, introspection functions (sim, mimic, actions.find/list, events.tail, registry inspection), and the Talon Python API (ui, clip, cron, screen, imgui, canvas, noise, fs modules)

## Testing

### Host REPL (read-only queries)

Use the host REPL only by piping one-shot commands into it for read-only queries — introspecting registries, testing command parsing with `sim()`, listing actions, etc. Do not open or rely on an interactive REPL session. Do NOT use the host REPL for testing behavioral changes that depend on specific application context.

- **Mac:** `~/.talon/bin/repl`
- **WSL:** `talon-repl`
- **Pattern:** `printf 'actions.list(\"user\")\n' | ~/.talon/bin/repl`

### talonbox (sandbox testing)

Use `talonbox` for tests that interact with the OS or with the user's Talon scripts — running `mimic()`, verifying side effects, capturing screenshots, or deploying scripts to a clean environment. It drives a macOS VM with a full Talon installation.

Run `talonbox --help` and `talonbox <command> --help` for usage details.

## Local Development

### Mac

- **Repo locations:** Talon repos live in `~/projects/` and are symlinked into `~/.talon/user/`
- **Auto-reload:** Changes to `.talon` and `.py` files are automatically reloaded within a few seconds
- **Logs:** `~/.talon/talon.log` (very long — don't read the whole thing). Changed files appear as `DEBUG [~] /path/to/file`, with possible `WARNING` or `ERROR` lines afterwards

#### Deploying Changes with `talon-install`

After making changes to a Talon repo, run `talon-install` to update the symlink in `~/.talon/user/` so Talon picks up the changes. Must be run from within the git repo.

**Usage:** `talon-install [--main] (--live | --snapshot)`

**Defaults:**
- **Main worktree** (running from it, or using `--main`): use `--live`
- **Alternate worktree**: use `--snapshot`

**Workflows:**

1. **Editing the main branch directly:**
   Run `talon-install --live` from the main worktree. The symlink points directly to the repo, so subsequent edits are picked up automatically.

2. **Testing a feature branch in a worktree:**
   Run `talon-install --snapshot` from the alternate worktree. A snapshot copy is made so further edits don't cause partial-state loading in Talon. Run it again after each batch of changes.

3. **Reverting to known-working state:**
   Run `talon-install --main --live` from any worktree of the repo. This points the symlink back at the main worktree, restoring stable behavior. Use this when a worktree's changes have broken something, then fix the issue and re-run `talon-install --snapshot` to test again.

**Notes:**
- The `cursorless-talon` and `cursorless-talon-dev` symlinks are managed separately and are not affected by `talon-install`
- Snapshots exclude `.git/` and `.venv/` to stay fast and small
- Only one snapshot per project is kept; old ones are cleaned up automatically

### WSL

- **Deploying changes:** Run `sync-talon-repo` to push the current repository to the Talon user directory in Windows
- **Auto-reload:** Talon takes a few seconds to load changed files after sync
- **Logs:** `/mnt/c/Users/james/AppData/Roaming/talon/talon.log` (very long — don't read the whole thing). Changed files appear as `DEBUG [~] c:\path\to\file`, with possible `WARNING` or `ERROR` lines afterwards

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/wolfmanstout) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
