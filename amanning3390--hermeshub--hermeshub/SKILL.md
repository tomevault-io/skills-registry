---
name: notion-integration
description: Read, create, and manage Notion pages, databases, and workspaces. Use when the user mentions Notion, knowledge base, notes, wiki, or document management. Use when this capability is needed.
metadata:
  author: amanning3390
---

# Notion Integration

Full Notion workspace access for reading, writing, and organizing content.

## When to Use
- User mentions Notion, pages, databases, or workspace
- User wants to create or update documentation
- User needs to search their knowledge base
- User wants to sync Notion content to local files

## Procedure
1. Verify Notion API key is configured
2. Search or navigate to the target page/database
3. Perform the requested operation (read/write/update)
4. Format output for readability
5. Confirm changes were applied

## Operations

### Search
- Search pages: Query by title or content
- List databases: Show all accessible databases
- Filter database: Query with filters and sorts

### Pages
- Read page: Extract full content with formatting
- Create page: Build from markdown or structured input
- Update page: Modify blocks, properties, or content
- Archive page: Soft-delete by archiving

### Databases
- Query: Filter, sort, and paginate results
- Create entry: Add new row with properties
- Update entry: Modify specific properties

## Pitfalls
- Notion API has rate limits (3 requests/second)
- Page content is returned as blocks — reassemble for readability
- Integration must be shared with target pages
- Rich text formatting may not convert perfectly to markdown

## Verification
- Confirm page exists and content matches
- Verify database entries have correct properties
- Check that shared pages are accessible

---
> Source: [amanning3390/hermeshub](https://github.com/amanning3390/hermeshub) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-30 -->
