---
name: lua
description: > Use when this capability is needed.
metadata:
  author: odjaramillo
---

## Critical Patterns

### Table Patterns (REQUIRED)

```lua
-- ✅ ALWAYS: Use tables for structured data
local user = {
    name = "John",
    age = 30,
    greet = function(self)
        print("Hello, " .. self.name)
    end
}

user:greet()  -- method call with self
```

### Module Pattern (REQUIRED)

```lua
-- ✅ ALWAYS: Return table for modules
local M = {}

function M.process(data)
    return data:upper()
end

return M
```

### Error Handling (REQUIRED)

```lua
-- ✅ Use pcall for protected calls
local ok, result = pcall(function()
    return risky_operation()
end)

if not ok then
    print("Error: " .. result)
end
```

---

## Decision Tree

```
Need class-like?           → Use metatables
Need error handling?       → Use pcall/xpcall
Need iteration?            → Use pairs/ipairs
Need configuration?        → Use external .lua files
```

---

## Commands

```bash
lua script.lua             # Run script
luac -o out.luac script.lua  # Compile
luarocks install package   # Install package
```

---

## Resources

- **Best Practices**: [best-practices.md](best-practices.md)
- **Performance**: [performance.md](performance.md)
- **Scripting**: [scripting.md](scripting.md)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/odjaramillo) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
