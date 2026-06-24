---
name: tig
description: tig text-mode git interface. Use for git browsing. Use when this capability is needed.
metadata:
  author: g1joshi
---

# Tig

Tig is an ncurses-based text-mode interface for git. It functions mainly as a Git repository browser.

## When to Use

- **Log Browsing**: `tig` shows the commit graph beautifully in the terminal.
- **Blame**: `tig blame file.txt` is faster than any GUI.
- **Staging**: `tig status` allows staging files quickly.

## Core Concepts

### Views

- **Main**: Commit graph.
- **Diff**: View changes.
- **Blame**: Who changed what.

### Navigation

Vim-like bindings (`j`, `k`) to scroll. `Enter` to drill down.

## Best Practices (2025)

**Do**:

- **Use as Pager**: Set `git config --global core.pager "tig"` (optional, but powerful).
- **Quick Blame**: Use it to find who broke the build in seconds.

**Don't**:

- **Don't use for complex writes**: Lazygit is better for rebasing/ops. Tig is better for reading.

## References

- [Tig Documentation](https://jonas.github.io/tig/)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/g1joshi) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
