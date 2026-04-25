---
name: upgrade-flake
description: | Use when this capability is needed.
metadata:
  author: richardgill
---

# Upgrade Flake Inputs

Update `flake.lock` to pull in the latest commits from all flake inputs. This gets you newer package versions without changing NixOS release channels.

## What this does

- Updates all inputs to their latest commits (nixpkgs, home-manager, etc.)
- Does NOT change release channels (stays on same NixOS version)
- Safe operation - can always rollback

## Process

1. **Update the lock file**
   ```bash
   just update
   ```
   This runs `nix flake update` and updates `flake.lock`.

2. **Build and verify** (safe dry-run)
   ```bash
   just check
   ```
   Builds all configurations without applying. If this fails, fix errors before proceeding.

3. **Apply the update** (when ready)
   ```bash
   just switch
   ```

4. **Verify services are running**
   ```bash
   systemctl status docker tailscaled
   ```

## Rollback if needed
When you're done echo out the instructions to:
```bash
# Revert to previous system generation
sudo nixos-rebuild switch --rollback

# Revert flake.lock to previous state
git checkout flake.lock
```

## Tips

- show the user what ch `git diff flake.lock` to see what changed before applying
- Old boot entries remain available for rollback until garbage collected

$ARGUMENTS

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/richardgill) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
