---
name: fusebase
description: Skill for interfacing with the Fusebase MCP server. Use this when the user wants to interact with Fusebase workspaces, pages, folders, tasks, tags, files, or members. Covers auth, caching, API discovery, and continuous improvement. Use when this capability is needed.
metadata:
  author: ryan-haver
---

# Fusebase MCP Skill

> Interface with Fusebase (formerly Nimbus Note) via the reverse-engineered MCP server at `c:\scripts\fusebase-mcp`.

---

## Known Workspaces (Cached)

| Name | Workspace ID | Org ID | Host |
|---|---|---|---|
| **Ryan** | `45h7lom5ryjak34u` | `u268r1` | `inkabeam.nimbusweb.me` |
| **Inkabeam** | `44ieqib7z0eltarr` | `u268r1` | `inkabeam.nimbusweb.me` |

> Full cache at `c:\scripts\fusebase-mcp\data\workspace_cache.json`

---

## Quick Reference

| What | Where |
|---|---|
| **MCP server** | `c:\scripts\fusebase-mcp\dist\index.js` |
| **Auth script** | `npx tsx scripts/auth.ts` (in project dir) |
| **Discovery script** | `npx tsx scripts/discover.ts` (in project dir) |
| **Config** | `c:\scripts\fusebase-mcp\.env` |
| **Workspace cache** | `c:\scripts\fusebase-mcp\data\workspace_cache.json` |
| **API log** | `c:\scripts\fusebase-mcp\data\api_log.jsonl` |

---

## Standard Operating Procedures

### SOP: Create a Page
1. **Resolve workspace** — Check "Known Workspaces" table above for ID. If not found, call `list_workspaces` to refresh.
2. **Optional: Resolve folder** — Call `list_folders` with workspace ID if placing in a specific folder.
3. **Create** — Call `create_page` with `workspaceId`, `title`, and optional `folderId`.
4. **Confirm** — Verify the response includes `globalId` and `title`.

### SOP: Read Workspace Content
1. **Resolve workspace** — Use cached ID from table above.
2. **List structure** — Call `list_folders` to get folder tree.
3. **List pages** — Call `list_pages` (optionally filtered by folder).
4. **Get detail** — Call `get_page` for specific page metadata.

### SOP: Auth Troubleshooting
1. **Auto-retry** — Server auto-retries on 401 via headless Playwright.
2. **Manual MCP** — Call `refresh_auth` with `interactive: true`.
3. **Manual CLI** — `cd c:\scripts\fusebase-mcp && npx tsx scripts/auth.ts`

---

## Caching Strategy

| Level | Cache | Duration | Rationale |
|---|---|---|---|
| **Workspaces** | `workspace_cache.json` | Persistent | Rarely change — ID + name + folder structure |
| **Folders** | `workspace_cache.json` | Persistent | Rarely change — cached per workspace |
| **Pages** | In-conversation only | Session | Change frequently — always fetch fresh |
| **Tasks** | In-conversation only | Session | Change frequently — always fetch fresh |
| **Tags/Labels** | `workspace_cache.json` | Persistent | Rarely change |

### Cache Refresh
Call `list_workspaces` to rebuild the full workspace + folder cache.

---

## Available MCP Tools (15)

### Auth
| Tool | Description |
|---|---|
| `refresh_auth` | Refresh session cookies via Playwright (headless or interactive) |

### Content
| Tool | Description |
|---|---|
| `list_workspaces` | List all workspaces in the org |
| `list_pages` | List pages in a workspace (filterable by folder) |
| `get_page` | Get detailed metadata for a specific page |
| `get_recent_pages` | Recently accessed pages |
| `create_page` | Create a new page in a workspace |
| `list_folders` | List all folders in a workspace |

### Files & Attachments
| Tool | Description |
|---|---|
| `get_page_attachments` | Get attachments for a page |
| `list_files` | List all files in a workspace |

### Metadata
| Tool | Description |
|---|---|
| `get_tags` | Workspace or page tags |
| `update_page_tags` | Set tags on a page |
| `get_labels` | Colored label categories |

### Organization
| Tool | Description |
|---|---|
| `get_members` | Workspace or org members |
| `get_org_usage` | Storage, member count, AI usage |
| `search_tasks` | Search tasks (by workspace/page) |

---

## Learnings Log

> Document discoveries during each interaction. Batch improvements and implement together.

### 2026-02-08 — Initial Build
- **Discovery**: Fusebase has no public API; internal endpoints work via cookie auth
- **Discovery**: POST requests to Fusebase API succeed even when Node.js terminal output appears to hang — the API is slow to respond but does work
- **Discovery**: Two pages were successfully created via `create_page` but response monitoring failed due to terminal output truncation
- **Pattern**: GET requests respond faster than POST requests to Fusebase API
- **Pattern**: Workspace IDs are 16-char alphanumeric strings
- **Improvement needed**: Better response logging so we can confirm operations succeed
- **Improvement needed**: Workspace ID caching to avoid repeat lookups

---

## Improvement Queue

| Priority | Improvement | Status |
|---|---|---|
| P0 | Workspace cache (avoid repeat lookups) | ✅ Implemented |
| P0 | API response logging | ✅ Implemented |
| P1 | Cache folders per workspace | ✅ In workspace cache |
| P1 | `update_page` tool (edit page content) | 🔲 Not yet |
| P1 | `delete_page` tool | 🔲 Not yet |
| P2 | `search_pages` tool (full-text search) | 🔲 Not yet |
| P2 | `move_page` tool (change parent folder) | 🔲 Not yet |

---

## Architecture

```
c:\scripts\fusebase-mcp\
├── src/
│   ├── index.ts       # MCP server (15 tools)
│   ├── client.ts      # HTTP client with 401 auto-retry + logging
│   └── types.ts       # TypeScript interfaces
├── scripts/
│   ├── auth.ts        # Playwright cookie capture
│   └── discover.ts    # API endpoint crawler
├── data/
│   ├── workspace_cache.json  # Workspace + folder cache
│   ├── cookie.json    # Cookie expiry metadata
│   └── api_log.jsonl  # API call history
├── .browser-data/     # Playwright persistent profile
└── dist/              # Compiled JS
```

## Extending the Server

1. Run `npx tsx scripts/discover.ts` to find new endpoints
2. Add methods to `src/client.ts`
3. Add types to `src/types.ts`
4. Register tools in `src/index.ts`
5. Compile: `npx tsc`
6. Update this skill file with new tools + learnings

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ryan-haver) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
