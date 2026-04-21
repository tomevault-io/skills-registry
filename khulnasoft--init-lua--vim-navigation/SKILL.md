---
name: vim-navigation
description: File navigation and fuzzy searching skills Use when this capability is needed.
metadata:
  author: khulnasoft
---

# Neovim Navigation Skills

This document covers file exploration, fuzzy searching, and buffer navigation.

---

## Telescope (`nvim-telescope/telescope.nvim`)

The primary fuzzy finder for Neovim.

### Keybindings
- `<leader>pf`: Find files in project.
- `<C-p>`: Find git files.
- `<leader>ps`: Grep string (prompts for input).
- `<leader>pws`: Grep current word under cursor.
- `<leader>pWs`: Grep current WORD under cursor.
- `<leader>vh`: Search help tags.

---

## Oil.nvim (`stevearc/oil.nvim`)

Edit your file system like a Neovim buffer.

### Keybindings
- `-`: Open Oil in the parent directory of the current file.

### Inside Oil Buffer
- `Enter`: Open file/directory.
- `<C-p>`: Preview file.
- `g.`: Toggle hidden files.
- `-`: Go to parent directory.
- `_`: Open current working directory.

---

## Netrw (Vim Built-in)

Legacy file explorer (minimalist).

### Keybindings
- `<leader>pv`: Open Netrw (`Ex` command).

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/khulnasoft) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
