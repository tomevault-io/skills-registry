---
name: vim-testing
description: Integrated testing with Neotest Use when this capability is needed.
metadata:
  author: khulnasoft
---

# Neovim Testing Skills

This document covers the testing capabilities in Neovim using `neotest`.

---

## Neotest Keybindings

Your configuration provides powerful shortcuts for running and inspecting tests:
- `<leader>tr`: Run nearest test.
- `<leader>ts`: Run test suite.
- `<leader>ta`: Run all tests (cwd).
- `<leader>td`: Debug nearest test (integrates with DAP).
- `<leader>tv`: Toggle summary UI.
- `<leader>to`: Open test output window.

---

## Supported Adapters

- **neotest-golang**: Specialized support for Go testing (configured with DAP integration).

---

## Neotest API Reference

```lua
local neotest = require("neotest")

-- Run tests
neotest.run.run()                    -- Nearest
neotest.run.run(vim.fn.expand("%"))  -- Current file
neotest.run.run({strategy = "dap"})  -- Run with debugger

-- UI & Output
neotest.summary.toggle()
neotest.output.open({ enter = true })
neotest.output_panel.toggle()
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/khulnasoft) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
