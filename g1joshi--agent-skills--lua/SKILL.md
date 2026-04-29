---
name: lua
description: Lua scripting for game development, embedded systems, and configuration. Use for .lua files. Use when this capability is needed.
metadata:
  author: g1joshi
---

# Lua

A powerful, efficient, lightweight, embeddable scripting language.

## When to Use

- Game Development (Roblox, WoW, Love2D)
- Embedded Systems
- Configuration scripting (Neovim, Nginx)
- Extending applications

## Quick Start

```lua
print("Hello, World!")

function factorial(n)
  if n == 0 then
    return 1
  else
    return n * factorial(n - 1)
  end
end
```

## Core Concepts

### Tables

The only complex data structure in Lua. Used as arrays, dictionaries, sets, and objects.

```lua
t = { key = "value", [1] = "first" }
print(t.key)
```

### Metatables

Allow changing the behavior of tables (e.g., operator overloading, inheritance).

### Indices

Arrays are 1-indexed (start at 1, not 0).

## Best Practices

**Do**:

- Use `local` variables by default (performance and scope)
- Use standard libraries where possible
- Understand table length operator `#` behavior with holes

**Don't**:

- Pollute the global namespace
- Ignore `nil` (undefined variables are `nil`)

## References

- [Lua 5.4 Reference Manual](https://www.lua.org/manual/5.4/)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/g1joshi) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
