---
name: banana-sync-to-notion
description: > Use when this capability is needed.
metadata:
  author: neversight
---

# Banana Sync to Notion

Automatically sync local Markdown files to Notion while preserving directory structure and full Markdown formatting.

## Setup

Before using this skill:

1. **Install dependencies** in the skill directory:
   ```bash
   cd banana-sync-to-notion
   npm install
   ```

2. **Configure environment variables** by creating a `.env` file:
   ```bash
   NOTION_TOKEN=your_notion_integration_token
   NOTION_ROOT_PAGE_ID=target_page_id
   ```

   - `NOTION_TOKEN`: Get from [Notion Integrations](https://www.notion.so/my-integrations)
   - `NOTION_ROOT_PAGE_ID`: The parent page ID where files will be synced
   - Ensure the integration has read/write permissions to the target page

## Usage

### Sync Files to Notion

Run from the skill directory:

```bash
npm run sync:notion
```

This command:
- Recursively scans the source directory (default: `Files/` in project root)
- Converts Markdown files to Notion blocks
- Preserves directory hierarchy as nested pages
- Assigns emoji icons based on filenames
- Skips existing pages (incremental sync)
- Shows detailed progress and statistics

### Clean Notion Pages

Remove all child pages under the target page:

```bash
npm run clean:notion
```

Use this before a fresh re-sync.

### Re-sync Everything

Clean existing content and sync fresh:

```bash
npm run resync:notion
```

Combines `clean:notion` + `sync:notion`.

## Markdown Support

All standard Markdown syntax is converted to native Notion blocks:

| Syntax | Notion Output |
|--------|---------------|
| `**bold**` or `__bold__` | **Bold text** |
| `*italic*` or `_italic_` | *Italic text* |
| `***bold italic***` | ***Bold italic*** |
| `` `code` `` | Inline code |
| `[text](url)` | Clickable link (http/https only) |
| ` ```language ... ``` ` | Code block |
| `- item` or `* item` | Bullet list |
| `1. item` | Numbered list |
| `> quote` | Quote block |
| `> 💡 note` | Callout (emoji-prefixed quotes) |
| `---` or `***` | Divider |
| Markdown tables | Native Notion tables |

**Relative Links**:
- `./file.md` or `../folder/file.md` → Converted to Notion page links
- `./image.png` → Preserved as text (no local file upload)
- `http://...` → Clickable external links

## Automatic Icon Selection

The script assigns emoji icons based on filename patterns:

- 📖 Chapters (e.g., "01-intro.md")
- 🎯 Getting started, basics
- ❓ FAQ, troubleshooting, problems
- 💡 Examples, case studies
- 🔧 Tools, utilities
- 📚 Methods, tutorials
- 📊 Data, analysis, reports
- ⚙️ Configuration, settings
- 🏗️ Architecture, system
- 💻 Scripts, code
- ✍️ Writing, creative
- 📝 Notes, records
- 🚀 Advanced, recommendations
- 📋 Guides
- 📘 README files
- 📦 Downloads, resources
- 🎨 Presentations
- 📄 Default (no match)

Customize icons by editing the `selectIcon` function in `scripts/sync-notion.js`.

## Smart Features

**Duplicate Detection**: Automatically skips pages that already exist with the same title, enabling incremental syncs without duplicates.

**Smart Chunking**:
- Handles Notion's 2000-character limit per block
- Supports files with >100 blocks
- Batches API requests to avoid rate limits
- Automatic retry on temporary failures

**Progress Reporting**: Shows detailed statistics during sync:
- Files processed
- Files created vs skipped
- Folders created
- Duration and errors

## Output Example

```
🚀 Starting Notion Sync...
📂 Source: /path/to/Files
📄 Target Page: My Knowledge Base

📁 Syncing directory: Files
  ✨ Creating: 📖 01-Introduction
  📁 Syncing directory: 01-Introduction
    ✨ Creating: 📖 01-overview.md
    ⏭️  Skipping existing: 02-concepts.md
    ✨ Creating: 🎯 03-quickstart.md

==================================================
✅ Sync Complete!
==================================================
⏱️  Duration: 45.2s
📊 Statistics:
   • Files processed: 35
   • Files created: 25
   • Files skipped: 10
   • Folders created: 5
   • Errors: 0
==================================================
```

## File Structure

```
banana-sync-to-notion/
├── SKILL.md
├── .env (user created)
├── package.json
├── scripts/
│   ├── sync-notion.js   # Main sync logic
│   └── clean-notion.js  # Cleanup utility
└── Files/ (default source directory)
```

## Troubleshooting

**Image support**: Current version focuses on text/Markdown. Images require hosted URLs (image hosting) to display in Notion. Local image upload requires more complex authentication.

**Missing icons**: Files that don't match any icon pattern use the default 📄 emoji. Add custom patterns in `scripts/sync-notion.js`.

**Rate limits**: The script includes automatic retry logic for Notion API rate limits. Large syncs (>100 files) may take several minutes.

**Relative links not working**: Ensure the linked Markdown file was also synced. Links only work to pages that exist in Notion.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neversight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
