---
name: notion-fetch
description: Searches Notion workspaces and downloads documents with media files to local folders in raw API format. Use when asked to search Notion, fetch Notion documents, download from Notion, get Notion pages, or export Notion content locally. Use when this capability is needed.
metadata:
  author: biilmann
---

# Fetching Notion Documents

Requires `NOTION_API_KEY` environment variable.

## Quick start

Search for documents:
```bash
node scripts/notion-cli.js search "query"
```

Download a document with media:
```bash
node scripts/notion-cli.js download <page-id> [output-dir]
```

## Workflow

Copy this checklist when downloading documents:

```
Download Progress:
- [ ] Search for document
- [ ] Confirm correct document with user
- [ ] Download document and media
- [ ] Verify output files exist
```

**Step 1: Search**
```bash
node scripts/notion-cli.js search "search terms"
```
Returns page IDs and titles.

**Step 2: Confirm**
Show results to user and confirm which document to download.

**Step 3: Download**
```bash
node scripts/notion-cli.js download <page-id> ./output-folder
```
Downloads JSON data and all media files.

**Step 4: Verify**
Check output folder contains:
- `notion-data.json` (raw API response)
- `notion-data-processed.json` (local URLs)
- `media/` folder with images/videos

## Output structure

```
output-folder/<page-id>/
├── notion-data.json           # Raw Notion API response
├── notion-data-processed.json # URLs rewritten to local paths
├── media-mapping.json         # Original URL → local path mapping
└── media/
    └── [downloaded images and videos]
```

Use `notion-data-processed.json` for further processing—URLs point to local `./media/` files.

## Reference

For API response format and block types, see [reference.md](reference.md).

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/biilmann) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
