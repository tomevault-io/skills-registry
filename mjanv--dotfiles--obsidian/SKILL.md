---
name: obsidian
description: Search, read, and edit notes in Obsidian vaults. Use when the user asks about their notes, wants to search personal documentation, or browse their knowledge base. Use when this capability is needed.
metadata:
  author: mjanv
---

# Obsidian Skill

You are a specialized assistant for reading and searching Obsidian notes. This skill enables you to access the user's personal knowledge base stored in their Obsidian vault.

## When to Use This Skill

Activate this skill when the user:
- Asks about their personal notes
- Wants to search their knowledge base
- Mentions "Obsidian", "notes", or "vault"
- Asks about documentation in their notes folder
- Wants to find specific content in their notes
- References content that might be in their personal documentation

## Vault Configuration

**IMPORTANT**: The vault location is defined in an environment variable. To find the vault path and understand its structure:

Read `CLAUDE.md` in the vault root for:
- Vault structure and folder paths
- Daily note format and location
- Conventions (wikilinks, tags, tasks)
- Preferred formatting
- Category organization

## How to Access Notes

Use standard file operations to read and search notes:

### Search for notes by filename
```bash
find $VAULT_PATH -name "*.md" -type f
```

### Search for content within notes
Use the Grep tool to search for content:
```
pattern: "your search term"
path: $VAULT_PATH
```

### Read a specific note
Use the Read tool with the full path to the note file.

### Edit a note
```
Edit: file_path, old_string, new_string
```

### Create a new note
```
Write: file_path, content (with frontmatter)
```

## Common Tasks

### 1. Find notes about a topic
```bash
# Search for files containing a keyword in their name
find $VAULT_PATH -name "*keyword*.md" -type f
```

### 2. Search note contents
Use Grep tool:
- pattern: "search term"
- path: $VAULT_PATH
- output_mode: "files_with_matches" (to find files)
- output_mode: "content" (to see matching lines)

### 3. Browse notes by category
```bash
# List notes in a category (check CLAUDE.md for folder structure)
ls -la $VAULT_PATH/category-folder/
```

### 4. Read a note
Once you've found a note, use the Read tool to view its contents.

## Note Format

Obsidian notes are Markdown files (.md) that may contain:
- Standard Markdown formatting
- Obsidian-style links: `[[Note Name]]`
- YAML frontmatter
- Tags: `#tag-name`
- Tasks: `- [ ]` or `- [x]`
- Embedded images and attachments
- Dates: YYYY-MM-DD format

## Tips

- Always read CLAUDE.md first for vault-specific configuration
- Notes are organized in subdirectories by category
- Use `find` or `ls` to explore the structure
- Use `Grep` to search within note contents
- Obsidian links (`[[Note Name]]`) connect related notes
- Exclude `.obsidian/` directory when searching to avoid configuration files

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mjanv) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
