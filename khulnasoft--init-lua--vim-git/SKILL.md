---
name: vim-git
description: Git integration and automation commands Use when this capability is needed.
metadata:
  author: khulnasoft
---

# Neovim Git Skills

This document covers the Git integration using Fugitive, Gitsigns, and custom utilities.

---

## Gitsigns.nvim (`lewis6991/gitsigns.nvim`)

Visual cues and hunk management in the sign column.

### Keybindings
- `]c`: Next hunk.
- `[c`: Previous hunk.
- `<leader>hs`: Stage hunk.
- `<leader>hr`: Reset hunk.
- `<leader>hp`: Preview hunk.
- `<leader>tb`: Toggle current line blame.
- `<leader>hd`: Diff this hunk.

---

## Fugitive (`tpope/vim-fugitive`)

The definitive Vim Git wrapper.

### Commands
- `<leader>gs`: Open Git status (Fugitive window).
- `gu`: Get from left (diffget //2).
- `gh`: Get from right (diffget //3).

### Inside Fugitive Window
- `<leader>p`: Push.
- `<leader>P`: Pull with --rebase.
- `<leader>t`: Push and set upstream.

---

## Custom "Dev Commit" Utility

Automated workflow for development commits.

- **Trigger**: `<leader>gc`
- **Action**: Stages all changes (`git add .`), commits with a timestamped "dev: auto-commit", and pushes to the current branch.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/khulnasoft) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
