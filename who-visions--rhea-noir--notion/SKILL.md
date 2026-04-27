---
name: notion
description: Comprehensive Notion API skill for Rhea-Noir. Covers Direct API, MCP integration, 2025 Data Model, Files, Comments, Templates, Search, Move Page, Webhooks, Rich Text, Link Previews, Rate/Size Limits, Token Management, and Enterprise Audit Logs. Use when this capability is needed.
metadata:
  author: who-visions
---

# Notion Skill (Rhea-Noir)

This skill allows Rhea-Noir to interact with Notion as the project's "Brain", specifically the **VeilVerse Database**. It covers connection methods, core workflows, and detailed implementation guides for the complete Notion API surface.

---

## 1. Connection Methods

### A. Direct API (Primary for Rhea)
Use the official Notion SDK (`notion-client` or `@notionhq/client`) for high-performance syncing.
*   **Best for**: Bulk sync, automated reports, complex database queries.
*   **Auth**: `NOTION_TOKEN` (Internal Integration Token).
*   **Access**: You MUST share specific pages/databases with the integration user (Three dots -> Connections).

### B. Notion MCP (Model Context Protocol)
Connect to Notion's hosted MCP server for AI-native interactions.
*   **Server URL**: `https://mcp.notion.com/mcp` (Streamable HTTP) or `https://mcp.notion.com/sse` (SSE fallback).
*   **Auth**: OAuth 2.0 with PKCE.

---

## 2. Rate Limits & Size Limits ⚠️

### Rate Limits
*   **Average**: 3 requests per second (some bursts allowed).
*   **429 Response**: Honor the `Retry-After` header (integer seconds).
*   **Strategy**: Use a queue system. Back off on 429s.

### Size Limits
*   **Max Payload**: 1000 block elements OR 500KB total.
*   **Rich Text Content**: 2000 characters.
*   **URLs**: 2000 characters.
*   **Emails/Phones**: 200 characters.
*   **Block Arrays**: 100 elements per request.
*   **Multi-select/Relations/People**: 100 options/pages/users.
*   **Equation Expression**: 1000 characters.

---

## 3. Search API 🔍

Find pages and data sources shared with your integration.
*   **Endpoint**: `POST https://api.notion.com/v1/search`
*   **SDK Method**: `notion.search({ query: "...", filter: { ... } })`

```python
# Python Example (services/notion.py)
results = await notion_service.search_by_title("Cyberpunk")
```

---

## 4. Working with Blocks 🧱

Everything in Notion is a Block.
*   **Endpoint**: `GET/PATCH https://api.notion.com/v1/blocks/{block_id}/children`
*   **SDK**: `notion.blocks.children.append({ block_id, children: [...] })`
*   **Limit**: Max 1000 blocks per request.

---

## 5. Rich Text Object 📝

The foundation for styled content.

### Structure
```json
{
  "type": "text",
  "text": { "content": "Hello ", "link": null },
  "annotations": { "bold": true, "italic": false, "strikethrough": false, "underline": false, "code": false, "color": "default" },
  "plain_text": "Hello ",
  "href": null
}
```

### Types
*   `"text"`: Plain text with optional link.
*   `"mention"`: Reference to User, Page, Database, Date, or Template.
*   `"equation"`: LaTeX expression.

### Annotations
| Property | Type | Description |
|---|---|---|
| `bold` | boolean | **Bold** |
| `italic` | boolean | *Italic* |
| `strikethrough` | boolean | ~~Strikethrough~~ |
| `underline` | boolean | Underline |
| `code` | boolean | `Inline code` |
| `color` | enum | `"default"`, `"blue"`, `"red_background"`, etc. |

---

## 6. Working with Databases & Pages 🗄️

### The 2025 Data Model (Database vs Data Source)
*   **Database**: Container object (title, description, icon, cover).
*   **Data Source**: The table holding rows. Schema (`properties`) lives here.
*   **Permission Check**: Use `isFullDatabase(db)` helper.

### Rhea Implementation
The `NotionService` class (`services/notion.py`) automatically discovers and uses the `data_source_id` for the VeilVerse database.

```python
from services.notion import NotionService
notion = NotionService()

# Query uses data source automatically
entities = await notion.query_veilverse(category=VeilVerseCategory.CHARACTER)

# Create uses data source automatically
await notion.create_entity(entity, content="...")
```

---

## 7. Move Page API 🚚

Relocate a page to a new parent.
*   **Endpoint**: `PATCH https://api.notion.com/v1/pages/{page_id}/move`
*   **Note**: Moving databases NOT supported.

```json
// Move to another Page
{ "parent": { "type": "page_id", "page_id": "<parent-page-id>" } }

// Move into a Database (Data Source)
{ "parent": { "type": "data_source_id", "data_source_id": "<data-source-id>" } }
```

---

## 8. Templates 📄

```python
# Use existing page as template when creating
await notion.pages.create({
  "parent": { "data_source_id": ds_id },
  "properties": { ... },
  "template": { "type": "template_id", "template_id": "EXISTING_PAGE_UUID" }
  # Note: Cannot specify `children` when using template.
})
```

---

## 9. Files & Media 📁

### Small File Upload (< 20MB)
```python
# 1. Create upload
file_upload = await notion.fileUploads.create({ "mode": "single_part" })

# 2. Send file
file_upload = await notion.fileUploads.send({
  "file_upload_id": file_upload.id,
  "file": { "filename": "image.png", "data": blob }
})
```

### Large File Upload (> 20MB)
1.  **Split**: 5-20MB parts (recommend 10MB). Final part can be < 5MB.
2.  **Create**: `notion.fileUploads.create({ mode: "multi_part", number_of_parts: N, filename: "..." })`
3.  **Send Parts**: Each with `part_number`. Can be parallel.
4.  **Complete**: `notion.fileUploads.complete({ file_upload_id: "..." })`

---

## 10. Comments 💬

```python
# Rhea's add_comment supports basic text
await notion_service.add_comment(page_id="...", text="Great work!")

# Full API can add display_name and attachments:
# {
#   "parent": { "page_id": "..." },
#   "rich_text": [{ "text": { "content": "Great work!" } }],
#   "display_name": { "type": "custom", "custom": { "name": "Rhea Noir" } },
#   "attachments": [{ "type": "file_upload", "file_upload_id": "..." }] # Max 3
# }
```

---

## 11. Status Codes

| Code | Error | Action |
| :--- | :--- | :--- |
| **400** | `validation_error` | Check structure against limits. |
| **401** | `unauthorized` | Check token. |
| **404** | `object_not_found` | Check permissions. |
| **409** | `conflict_error` | Retry. |
| **429** | `rate_limited` | Honor `Retry-After` header. |
| **500** | `internal_server_error` | Retry with backoff. |

---

## 12. Rhea Usage Examples

### Search VeilVerse
```python
from services.notion import NotionService
notion = NotionService()
entities = await notion.search_veilverse("Cyberpunk")
```

### Create Entity
```python
from models.veilverse import VeilEntity, VeilVerseCategory
entity = VeilEntity(
    name="Neo-Tokyo",
    category=VeilVerseCategory.LOCATION,
    description="A futuristic city in the Near Future Era."
)
await notion.create_entity(entity, content="Full city description here...")
```

### Add Comment
```python
await notion.add_comment(page_id="page_id_here", text="Rhea verified this entry.")
```

---

## 13. Configuration

Requires `.env`:
- `NOTION_TOKEN`: Integration token
- `DATABASE_ID`: (Hardcoded in service) VeilVerse Database ID

---

## 14. Resources
*   **LLM Index**: [developers.notion.com/llms.txt](https://developers.notion.com/llms.txt)
*   **Notion Cookbook**: [github.com/makenotion/notion-cookbook](https://github.com/makenotion/notion-cookbook)
*   **MCP SDK**: [github.com/modelcontextprotocol/sdk](https://github.com/modelcontextprotocol/sdk)
*   **2025 Upgrade Guide**: [developers.notion.com/guides/get-started/upgrade-guide-2025-09-03](https://developers.notion.com/guides/get-started/upgrade-guide-2025-09-03.md)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/who-visions) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
