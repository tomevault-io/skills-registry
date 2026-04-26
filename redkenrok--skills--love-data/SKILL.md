---
name: skill-name
description: {{SKILL_DESCRIPTION}} Use this skill when working with data operations, encoding/decoding, compression, data transformation, or any data-related operations in LÖVE games. Use when this capability is needed.
metadata:
  author: redkenrok
---

## When to use this skill
{{SKILL_DESCRIPTION}} Use this skill when working with data operations, encoding/decoding, compression, data transformation, or any data-related operations in LÖVE games.

## Common use cases
- Encoding and decoding data formats
- Compressing and decompressing data
- Working with binary data and byte arrays
- Performing data transformations and conversions
- Handling game save data and serialization

{{MODULES_LIST}}
{{FUNCTIONS_LIST}}
{{CALLBACKS_LIST}}
{{TYPES_LIST}}
{{ENUMS_LIST}}

## Examples

### Data encoding
```lua
-- Encode data to base64
local originalData = "Hello World!"
local encoded = love.data.encode("string", "base64", originalData)
print(encoded)

-- Decode base64 data
local decoded = love.data.decode("string", "base64", encoded)
print(decoded)  -- "Hello World!"
```

### Data compression
```lua
-- Compress game data
local gameData = serializeGameState()
local compressed = love.data.compress("string", "zlib", gameData)

-- Save compressed data
love.filesystem.write("savegame.dat", compressed)
```

## Best practices
- Use appropriate encoding formats for different data types
- Consider compression for large data sets
- Handle data encoding/decoding errors gracefully
- Test data operations on target platforms
- Be mindful of memory usage with large data operations

## Platform compatibility
- **Desktop (Windows, macOS, Linux)**: Full data support
- **Mobile (iOS, Android)**: Full support
- **Web**: Full support

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/redkenrok) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
