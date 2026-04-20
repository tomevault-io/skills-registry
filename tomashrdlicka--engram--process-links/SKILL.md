---
name: process-links
description: Process all queued links from the web capture queue into Obsidian notes. Use when the user wants to process their captured links. Use when this capability is needed.
metadata:
  author: tomashrdlicka
---

# process-links

Process all queued links from the web capture queue.

## Usage
```
/process-links
/process-links --limit 5
/process-links --topic ai-ml
```

## Options
- `--limit N` - Process only the first N links
- `--topic TOPIC` - Only process links that would go to this topic
- `--dry-run` - Show what would be processed without doing it

## Instructions

When the user invokes `/process-links`, follow these steps:

### Step 0: Load Configuration
Read `config.json` from the engram project root to get the vault path:
```json
{
  "vault_path": "~/Documents/Obsidian/WebCapture"
}
```
Use the `vault_path` value as `{VAULT_PATH}` in all paths below. Expand `~` to the user's home directory.

### Step 1: Read Queue and Vault Context
Read both files:
- `data/queue.json`
- `{VAULT_PATH}/_system/index.json` (for connection discovery)

### Step 2: Filter Links and Deduplicate
- Only process links with `status: "pending"`
- **Deduplicate against vault**: For each pending link, check if its URL (normalized: strip trailing slash, fragment, tracking params like utm_*, ref, fbclid) already exists in `index.json` notes. If a match is found, mark the queue item as `status: "processed"` with `note_path` pointing to the existing note and `skipped_reason: "duplicate"`, then skip it. Report skipped duplicates in the summary.
- Apply `--limit` if specified
- Apply `--topic` filter after determining topic (if specified)

### Step 3: Process Each Link
For each pending link:

1. Report: "Processing: {title} ({url})"

2. Use the same logic as `/capture`:
   - Fetch content with WebFetch
   - Determine type and topic
   - Generate summary, key points, and **enriched tags** (6-12 tags, analyzing content beyond user-provided tags)
   - **Find related notes** - search index for notes sharing 2+ tags, same topic, or overlapping themes. Select top 3.
   - **Add bidirectional wiki-links** - in the new note's `## Connections` section, add wiki-links with prose context. Read each related note file and append a backlink.
   - Create note file with `## Connections` section
   - Update index (including `related` array on new note AND on related notes' entries)
   - Update topic page `## Notes` section

3. If `user_context` is present in the queue item, use it to guide summarization

   **Intent detection** (per-link):
   - If queue item has `share_twitter: true`, add tag `share-twitter` and set `share_intent: "twitter"` on the index entry
   - If queue item has `deep_learn: true`, add tag `deep-learn` and set `deep_learn: true` on the index entry
   - **Auto-detection fallback**: If `user_context` contains phrases like "post on X", "post on twitter", "share on twitter", "postable on X", "tweet this", "share this", set `share_intent: "twitter"`. If it contains "learn", "study", "deep dive", "important", "reread", "want to learn", "understand this", set `deep_learn: true`

4. After successful processing:
   - Update the queue item's status to "processed"
   - Add `processed_at` timestamp
   - Add `note_path` with the created note path

5. If processing fails:
   - Update status to "failed"
   - Add `error` field with reason
   - Continue to next link

### Step 4: Save Updated Queue
Write the updated queue back to `data/queue.json`

### Step 5: Regenerate Views
After all links are processed (not per-link), regenerate the 4 view files once from `index.json` data:

1. **`views/by-date.md`** - List all notes sorted by capture date (newest first). Group by date. Each entry: `- [{type}] [[{note_path}|{title}]] - {summary snippet}`

2. **`views/by-type.md`** - Group notes by content type (article, video, x_post, photo, tool, research, quick). Under each heading, list notes sorted by date.

3. **`views/unread.md`** - List only notes where `read: false`, sorted by date. Each entry includes title, type, topic, and capture date.

4. **`views/favorites.md`** - List only notes where `favorite: true`, sorted by date.

5. **`views/twitter-queue.md`** - Notes with `share_intent: "twitter"` and `twitter_posted != true`. Each entry: title with source URL link, 1-2 sentence summary, suggested tweet angle based on key_points/user_context, tags for hashtag inspiration. Header shows count.

6. **`views/learnings.md`** - Notes with `deep_learn: true`, grouped by topic. Each topic group shows: topic name with note count, each note with title/summary/connections to other learning resources. Footer suggests reading order.

Read `{VAULT_PATH}/_system/index.json`, filter/sort the `notes` array, and write each view file. Use Obsidian wiki-links (`[[path|title]]`).

### Step 5b: Git Sync Vault
After all links are processed and views regenerated, push vault changes to git:
```bash
cd {VAULT_PATH}
git add -A
git commit -m "vault: processed N links (YYYY-MM-DD)"
git push origin main
```
If the vault is not a git repo yet, skip this step silently.

### Step 6: Report Summary
Tell the user:
```
Processed X links:
- [title1] -> content/topic/note.md (connected to: note-a, note-b)
- [title2] -> content/topic/note.md (connected to: note-c)

Failed: Y links
- [title3]: error reason

Remaining in queue: Z links
```

## Dry Run Mode
If `--dry-run` is specified:
1. Fetch and analyze each link
2. Show what would be created (type, topic, enriched tags, likely connections)
3. Don't create files or update queue
4. Report the plan

## Error Handling
- If queue file doesn't exist, create it with empty array
- If a link fails, log the error and continue with remaining links
- Report all failures at the end

## Notes
- Queue file: `data/queue.json`
- Vault: `{VAULT_PATH}/`
- Use the capture skill logic for each link, including connection discovery
- Always read the vault CLAUDE.md at `{VAULT_PATH}/CLAUDE.md` for vault conventions

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tomashrdlicka) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
