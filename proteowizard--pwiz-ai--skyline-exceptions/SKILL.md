---
name: skyline-exceptions
description: Use this skill when looking at exception reports from skyline.ms.
metadata:
  author: proteowizard
---

# Skyline Exception Triage

When working with Skyline exception data from skyline.ms, consult these documentation files.

## Core Documentation

1. **ai/docs/mcp/exceptions.md** - Complete system documentation
   - Architecture and components
   - MCP tools reference
   - Data schema
   - Setup instructions

## When to Read What

- **Before querying exceptions**: Read ai/docs/mcp/exceptions.md (MCP tools section)
- **For daily triage**: Read ai/docs/mcp/exceptions.md (Daily triage workflow)
- **For setup/debugging**: Read ai/docs/mcp/exceptions.md (Setup section)
- **For code changes to MCP server**: Read ai/mcp/LabKeyMcp/README.md

## Quick Reference

**Data location**: skyline.ms → /home/issues/exceptions → announcement.Announcement

**MCP tools available**:
- `query_exceptions(days, max_rows)` - Recent exceptions
- `get_exception_details(exception_id)` - Full stack trace
- `list_schemas`, `list_queries`, `list_containers` - Discovery
- `query_table` - Generic queries

**Title format**:
```
ExceptionType | FileName.cs:line N | Version | InstallIdSuffix
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/proteowizard) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
