---
name: test-lua
description: Run Lua/Neovim plugin tests Use when this capability is needed.
metadata:
  author: unknownbreaker
---

Run the Lua plugin test suite using plenary.nvim.

## Usage

`/test-lua` - Run all Lua tests
`/test-lua server` - Run only server tests
`/test-lua config` - Run only config tests

## Commands

Run all Lua tests:
```bash
make test-unit
```

Run filtered tests (replace FILTER with test name pattern):
```bash
nvim --headless --noplugin -u tests/minimal_init.lua \
  -c "lua require('plenary.test_harness').test_directory('tests/lua/', {minimal_init = 'tests/minimal_init.lua', filter = '$ARGUMENTS'})" \
  -c "qa!"
```

After running tests, report:
1. Total tests run
2. Pass/fail count
3. Any failing test details with file:line references

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/unknownbreaker) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
