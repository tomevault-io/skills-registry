---
name: nvim-context
description: > Use when this capability is needed.
metadata:
  author: majjoha
---

# Neovim context provider
## Purpose
Provides live context from the user's Neovim editor session to help answer
context-aware questions about code.

## How it works
1. Executes the `nvim-context` tool to get the current editor state.
2. Returns JSON data including cursor position, open file, visual selection and
   diagnostics.
3. Use this information to understand references like "this line", "the
   selection", "current file", etc.

## Usage examples
- "What's wrong with this line?" → Check diagnostics at cursor
- "Explain the selected code" → Analyze visual selection
- "What file am I in?" → Return current file path
- "Show me all errors" → List all LSP diagnostics

## Technical details
To use this skill, execute the `nvim-context` CLI command which outputs JSON:
```json
{
  "cursor": {
    "line": 43,
    "col": 3
  },
  "file": "/path/to/current/file.rb",
  "selection": null,
  "diagnostics": []
}
```

## Implementation
When this skill is loaded, execute `nvim-context` via Bash and parse the JSON
output to understand the current editor state. Use the returned data to answer
user questions about their code.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/majjoha) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
