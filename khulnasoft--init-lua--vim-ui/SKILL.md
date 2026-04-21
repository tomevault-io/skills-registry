---
name: vim-ui
description: UI enhancements, themes, and specialized views Use when this capability is needed.
metadata:
  author: khulnasoft
---

# Neovim UI & Utilities Skills

This document covers the visual enhancements and utility tools in your Neovim environment.

---

## Trouble.nvim (`folke/trouble.nvim`)

A pretty list for showing diagnostics, references, telescope results, etc.

### Keybindings
- `<leader>tt`: Toggle Trouble window.
- `[t`: Next trouble item.
- `]t`: Previous trouble item.

---

## Zen Mode (`folke/zen-mode.nvim`)

Distraction-free coding environment.

### Keybindings
- `<leader>zz`: Toggle Zen Mode (90 width, relative numbers ON).
- `<leader>zZ`: Toggle Super Zen Mode (80 width, numbers OFF).

---

## Undotree (`mbbill/undotree`)

Visualizes the undo history as a tree.

### Keybindings
- `<leader>u`: Toggle Undotree.

---

## Cloak.nvim (`laytan/cloak.nvim`)

Automatically masks sensitive information (like API keys) in `.env` files.

### Features
- Masks passwords/secrets with `*`.
- Configured for `.env*`, `wrangler.toml`, and `.dev.vars`.

---

## Themes & Colors

### ColorMyPencils
Your custom function to apply colors and handle transparency.
- Default: `rose-pine-moon`
- Available: `tokyonight`, `gruvbox`, `rose-pine`.

---

## Markdown & Preview

### Peek.nvim
Markdown previewer for Neovim.

---

## Startup & Appearance

### Alpha-nvim
A high-speed, fully customizable dashboard for Neovim.
- Shows a "Neovim" ASCII header on startup.
- Uses the `startify` theme for quick access.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/khulnasoft) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
