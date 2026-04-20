---
name: user-files
description: Search and read user's uploaded files (PDFs, markdown, images, documents) Use when this capability is needed.
metadata:
  author: mixedbread-ai
---

# User Files Access

You have access to the user's uploaded files stored in `/files/`. These can include:
- PDF documents
- Markdown files (.md)
- Text files (.txt)
- Word documents (.docx)
- Images (PNG, JPG, GIF, WebP)

## How to access files

Use the `files` tool with these commands:

### Search files semantically
```
files(command="search", query="your search query", path="/files/")
```
This performs semantic search across all uploaded files. Use natural language queries.

### List files in a folder
```
files(command="list", path="/files/")
files(command="list", path="/files/subfolder/")
```

### Read a specific file
```
files(command="read", path="/files/document.md")
```

## Tips

1. **Start with search** - If the user asks about a topic, search first to find relevant files
2. **Check file types** - The search results include metadata with mime_type
3. **For images** - You can describe what's in an image path but cannot view images directly
4. **Folder structure** - Users can organize files in folders, use list to explore

## Example queries

- "What did that PDF say about quarterly results?" → Search for "quarterly results" in /files/
- "Summarize my notes on machine learning" → Search for "machine learning" then read matching files
- "What files do I have?" → List /files/

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mixedbread-ai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-12 -->
