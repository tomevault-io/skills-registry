---
name: read-codebase
description: Explore and understand code structure efficiently using semantic navigation. Use when exploring unfamiliar code, understanding architecture, finding symbol relationships, or investigating how something works. Use when this capability is needed.
metadata:
  author: devitools
---

# Semantic Code Exploration

This skill guides efficient code exploration using **Serena MCP** for semantic navigation.

## Prerequisite Check

Before proceeding, verify Serena is available by checking for `mcp__serena__*` tools.

**If Serena is NOT configured:**
Tell the user: "This exploration would benefit from Serena MCP for semantic code navigation, but it's not configured. See `.claude/skills/read-codebase/setup.md` for installation instructions. Proceeding with standard tools (Grep/Read/Glob)."

Then use standard tools as fallback.

## Tool Priority (when Serena is available)

1. **get_symbols_overview** - First step for any file
2. **find_symbol** - Locate specific classes/methods
3. **find_referencing_symbols** - Find all usages
4. **search_for_pattern** - When symbol name is unknown
5. **read_memory** - Check project knowledge (architecture, patterns)

## Quick Reference

### Understanding a File
1. `get_symbols_overview(relative_path="path/to/file.php")` → see structure
2. `find_symbol(name_path_pattern="ClassName", depth=1)` → see methods
3. Only use `include_body=true` for specific methods you need

### Finding Where Code Is Used
1. `find_symbol` to locate the symbol
2. `find_referencing_symbols` to find all call sites

### Exploring Unknown Code
1. Check memories: `list_memories` → `read_memory("architecture")`
2. Use `search_for_pattern` with keywords
3. Follow up with symbolic tools

## Anti-Patterns

- Reading entire files when you only need one method
- Using Grep/Read when Serena tools would be more precise
- Skipping symbol overview and going straight to file read
- Not checking project memories for existing documentation

## When to Use Standard Tools Instead

- Simple text search across many files → Grep
- Config files, markdown, non-code → Read
- Known file needing full content → Read
- Serena not configured → Fallback to Grep/Read/Glob

## Additional Resources

- Detailed workflows: [workflows.md](workflows.md)
- Setup instructions: [setup.md](setup.md)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/devitools) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
