---
name: vim
description: Core Neovim navigation and editing skills Use when this capability is needed.
metadata:
  author: khulnasoft
---

# Neovim Core API Reference

This document covers the essential Neovim Lua APIs for automation and configuration.

---

## vim.api

The main interface for calling Neovim's internal C functions.

```lua
-- Buffer operations
vim.api.nvim_get_current_buf()
vim.api.nvim_buf_get_lines(bufnr, start, end_, strict)
vim.api.nvim_buf_set_lines(bufnr, start, end_, strict, lines)

-- Window operations
vim.api.nvim_get_current_win()
vim.api.nvim_win_get_cursor(win)
vim.api.nvim_win_set_cursor(win, {row, col})

-- Keymaps & Commands
vim.api.nvim_set_keymap(mode, lhs, rhs, opts)
vim.api.nvim_create_user_command(name, command, opts)
vim.api.nvim_create_autocmd(event, opts)
vim.api.nvim_create_augroup(name, opts)
```

---

## vim.fn

Access to legacy Vimscript functions.

```lua
vim.fn.expand("%:p")        -- Get current file absolute path
vim.fn.line(".")            -- Get current line number
vim.fn.stdpath("config")    -- Get config directory path
vim.fn.jobstart(cmd, opts)  -- Start an asynchronous job
```

---

## khulnasoft Custom Utilities

### Git Automation (`<leader>gc`)
Your configuration includes a custom `dev_commit` utility that:
1. Runs `git add .`
2. Commits with a timestamped message.
3. Pushes to origin asynchronously.

### Reloading (`R`)
Use the global `R(module_name)` function to reload Lua modules without restarting Neovim.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/khulnasoft) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
