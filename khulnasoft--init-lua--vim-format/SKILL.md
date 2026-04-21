---
name: vim-format
description: Code formatting and linting integration Use when this capability is needed.
metadata:
  author: khulnasoft
---

# Neovim Formatting Skills

This document covers code formatting using `conform.nvim`.

---

## Conform.nvim (`stevearc/conform.nvim`)

Lightweight yet powerful formatter plugin.

### Keybindings
- `<leader>f`: Format current buffer (configured in `remap.lua`).

### Configuration
- **Lua**: Uses `stylua`.
- **Global Config**: Managed via `formatters_by_ft`.

### API Reference
```lua
require("conform").format({
  bufnr = 0,
  async = false,
  lsp_fallback = true,
})
```

---

## Static Analysis (Luacheck)

Your project includes a `.luacheckrc` and is configured for clean Lua development.

- Run check: `luacheck .`
- Global variables like `vim`, `ColorMyPencils`, and `R` are whitelisted.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/khulnasoft) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
