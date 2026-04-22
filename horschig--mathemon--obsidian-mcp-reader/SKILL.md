---
name: obsidian-mcp-reader
description: Guide for reading and accessing Obsidian notes and documentation using Obsidian MCP tools. Use when retrieving content from Obsidian vaults for research, documentation, or cross-referencing information stored in the vault, particularly for game lore, species descriptions, and design documents. Use when this capability is needed.
metadata:
  author: horschig
---

# Obsidian MCP Reader

This skill provides guidance for using Obsidian MCP tools to read and access notes from Obsidian vaults.

## Available Tools

### mcp_obsidian_read_notes
Reads the contents of multiple notes. Each note's content is returned with its path as a reference.

**Parameters:**
- `paths`: Array of note paths to read (e.g., ["Species/Alpine.md", "Design/Game Mechanics.md"])

**Usage:**
- Use when you need the full content of specific notes
- Failed reads for individual notes won't stop the entire operation
- Reading too many notes at once may result in an error

### mcp_obsidian_search_notes
Searches for notes by name. The search is case-insensitive and matches partial names. Queries can also be valid regex.

**Parameters:**
- `query`: The search term or regex pattern

**Usage:**
- Use to find notes when you don't know the exact path
- Returns paths of matching notes
- Useful for discovering related documentation

## Project-Specific Usage

In this BRUTAL Sports Manager project, the Obsidian vault is located at:
`C:\Users\intel\Documents\Obsidian\First one`

### Common Use Cases

1. **Species Research**: Read species descriptions and traits from notes in the vault
2. **Lore and Narrative**: Access game world lore and story elements
3. **Design Documents**: Retrieve design decisions and game mechanics documentation
4. **Cross-Referencing**: Link gameplay elements with their narrative context

### Workflow

1. **Search First**: Use `mcp_obsidian_search_notes` to find relevant notes
2. **Read Specific**: Use `mcp_obsidian_read_notes` to get full content of identified notes
3. **Integrate**: Use the retrieved information in code comments, documentation, or implementation

### Best Practices

- Start with search to discover available notes before reading
- Read multiple related notes in a single call when possible
- Use regex in search queries for complex patterns
- Note paths are relative to the vault root

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/horschig) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
