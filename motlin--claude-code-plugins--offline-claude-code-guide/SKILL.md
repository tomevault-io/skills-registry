---
name: offline-claude-code-guide
description: Offline Claude Code documentation fallback. Use when the user asks about Claude Code features, capabilities, or configuration and the built-in claude-code-guide subagent fails due to network issues or proxy blocking. Use when this capability is needed.
metadata:
  author: motlin
---

# Offline Claude Code Guide

This skill provides offline access to Claude Code documentation when the built-in `claude-code-guide` subagent cannot fetch documentation from the network.

## Documentation Location

All Claude Code documentation is available locally. First read the documentation map at `docs/claude_code_docs_map.md` to find which file contains the relevant information.

## How to Answer Questions

1. Read [claude_code_docs_map.md](../../docs/claude_code_docs_map.md) to identify which documentation file contains the relevant information
2. Read the appropriate documentation file(s) from `docs/`
3. Provide accurate information based on the official documentation
4. Include relevant examples and code snippets from the docs

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/motlin) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
