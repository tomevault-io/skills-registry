---
name: vault-search
description: Search the Web Capture Obsidian vault for ideation and research. Use when the user wants to find or explore saved content. Use when this capability is needed.
metadata:
  author: tomashrdlicka
---

# vault-search

Search the Web Capture Obsidian vault for ideation and research.

## Usage
```
/vault-search <query>
/vault-search "machine learning agents"
/vault-search --topic ai-ml
/vault-search --tags prompting,llm
/vault-search --unread
```

## Options
- `--topic TOPIC` - Filter by topic folder
- `--tags TAG1,TAG2` - Filter by tags (comma-separated)
- `--type TYPE` - Filter by content type (article, video, tool, research, x_post)
- `--unread` - Only show unread items
- `--starred` - Only show starred items
- `--limit N` - Limit results (default: 10)
- `--since DATE` - Only items captured after date (YYYY-MM-DD)
- `--move <note> <new-topic>` - Move a note to a different topic (auto-updates all wiki-links)

## Instructions

When the user invokes `/vault-search`, follow these steps:

### Step 0: Load Configuration
Read `config.json` from the engram project root to get the vault path:
```json
{
  "vault_path": "~/Documents/Obsidian/WebCapture"
}
```
Use the `vault_path` value as `{VAULT_PATH}` in all paths below. Expand `~` to the user's home directory.

### Step 1: Read the Index
Read `{VAULT_PATH}/_system/index.json`

This contains all notes with searchable metadata:
- `id`, `path`, `title`, `type`, `topic`
- `url`, `source_domain`, `captured`
- `read`, `starred`, `tags`, `summary`
- `key_points`, `user_context`

### Step 2: Parse Query and Options
Extract:
- Free-text query (search in title, summary, tags, key_points, user_context)
- Filter options (topic, tags, type, unread, starred, since)

### Step 3: Search and Filter
Apply filters in order:
1. Filter by `--topic` if specified
2. Filter by `--tags` if specified (match any)
3. Filter by `--type` if specified
4. Filter by `--unread` if specified (read === false)
5. Filter by `--starred` if specified
6. Filter by `--since` if specified
7. Search free-text query across text fields

For text search, check if query appears in (case-insensitive):
- `title`
- `summary`
- `tags` (any tag)
- `key_points` (any point)
- `user_context`

### Step 4: Rank Results
Order by relevance:
1. Exact match in title (highest)
2. Match in user_context (user's intent)
3. Match in summary
4. Match in tags
5. Match in key_points
6. By capture date (recent first) for ties

### Step 5: Format Results
For each result, show:
```
## [Title](path)
**Type:** article | **Topic:** ai-ml | **Captured:** 2026-02-05

> Summary text here

**Tags:** #tag1 #tag2 #tag3
**Why saved:** User context if available
```

### Step 6: Offer Actions
After showing results, offer:
- "Want me to read any of these in full?"
- "Should I synthesize insights across these?"
- "Want to see related notes?"
- "Want to move any of these to a different topic?"

### Step 7: Move Note (if --move)
If the user requests moving a note to a different topic, use `obsidian-cli move` which automatically updates all wiki-links across the vault:

```bash
obsidian-cli move "content/{old-topic}/{filename}" "content/{new-topic}/{filename}"
```

After moving:
1. Update `index.json` - change the note's `topic` and `path` fields
2. Update the old topic's `_topic.md` - remove the note entry
3. Update the new topic's `_topic.md` - add the note entry
4. Regenerate views (by-type, by-date, etc.)
5. Git sync vault

## Synthesis Mode
If user asks to synthesize (e.g., "synthesize insights about X"):

1. Find all relevant notes using search
2. Read each note file in full
3. Extract key insights, patterns, themes
4. Create a synthesis that:
   - Identifies common threads
   - Notes contradictions or debates
   - Highlights actionable takeaways
   - Suggests connections the user might not have seen

## Notes
- Vault: `{VAULT_PATH}/`
- Index: `_system/index.json`
- For quick lookups, use index only (no file reads)
- For deep dives, read actual note files or `obsidian-cli print "{note-path}"`
- `obsidian-cli move` handles wiki-link rewiring automatically when reorganizing notes
- `obsidian-cli frontmatter "{note-path}" --print` is a quick way to inspect a note's metadata

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tomashrdlicka) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
