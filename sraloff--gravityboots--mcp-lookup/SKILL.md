---
name: mcp-lookup
description: Loads and searches mounted external documentation for framework/library details. Use when this capability is needed.
metadata:
  author: sraloff
---

# MCP Lookup Skill

When activated:
- Scan /docs/mcp/ for matching files/subfolders (e.g., /docs/mcp/livewire-v3.md for "Livewire forms")
- Search for keywords in prompt (e.g., "validation" → grep for it in livewire files)
- Return 1–3 relevant snippets with source path:
  "From /docs/mcp/livewire-v3.md: 'Livewire forms use wire:model for two-way binding.'"
- If multiple matches → list options and ask which to use
- If no match → "No matching docs found in /docs/mcp/. Want to add [suggested file]?"

Keep output concise: quote + source path + 1-line explanation.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sraloff) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
