---
name: notion-search
description: Search Notion documentation and knowledge base from the Comfy-Org team workspace. Use when the user needs to find internal documentation, guides, meeting notes, or reference materials stored in Notion. Use when this capability is needed.
metadata:
  author: comfy-org
---

# Notion Documentation Search

This skill searches the Comfy-Org team Notion workspace for documentation, guides, and internal knowledge.

## Usage

```bash
prbot notion search -q "<SEARCH_TERMS>" [-l <NUMBER>]
```

## Parameters

- `-q, --query` (required): Search terms to find in Notion pages
- `-l, --limit` (optional): Maximum number of results to return (default: 10)

## Output Format

Returns results with:

- **Title**: Page title
- **URL**: Direct link to Notion page
- **Last edited**: When the page was last modified
- **Page ID**: Unique identifier

## Examples

```bash
# Search for ComfyUI setup documentation
prbot notion search -q "ComfyUI setup" -l 5

# Find meeting notes
prbot notion search -q "weekly sync meeting"

# Search for API references
prbot notion search -q "API documentation workflow" -l 3
```

## Notes

- Requires Notion API integration token in environment variables
- Searches across all accessible pages in the Comfy-Org workspace
- Returns most recently edited pages first
- Useful for finding internal documentation not in public docs

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/comfy-org) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
