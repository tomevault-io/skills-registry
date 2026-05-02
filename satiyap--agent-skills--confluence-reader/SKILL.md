---
name: confluence-reader
description: Read Confluence Cloud pages as markdown, browse page trees, download image attachments, and diff local content against Confluence pages. Use when the user mentions Confluence, wiki pages, Confluence documentation, or needs to compare local docs with Confluence. Use when this capability is needed.
metadata:
  author: satiyap
---

# Confluence Reader

Read and interact with Confluence Cloud pages by running `scripts/confluence.py`.

## Prerequisites

These environment variables must be set:

- `CONFLUENCE_TOKEN` — Scoped API token
- `CONFLUENCE_EMAIL` — Email tied to the Atlassian account
- `CONFLUENCE_CLOUD_ID` or `CONFLUENCE_BASE_URL` — one is required

If any are missing, prompt the user to set them before proceeding.

## Commands

All commands are run via:

```bash
python scripts/confluence.py <command> [args...]
```

### Fetch a page as markdown

```bash
python scripts/confluence.py fetch-page "<confluence-page-url>"
```

Returns the page content as markdown with child pages listed at the bottom.

### List child pages

```bash
python scripts/confluence.py list-children "<confluence-page-url>"
```

Returns titles and IDs of direct child pages without fetching content.

### Download an image attachment

```bash
python scripts/confluence.py fetch-image "<confluence-page-url>" "<filename>" "<destination-dir>"
```

Downloads the named attachment and saves it to the destination directory.

### Compare local content with a Confluence page

```bash
python scripts/confluence.py compare "<confluence-page-url>" "<local-markdown-or-filepath>"
```

The last argument can be a file path or a raw markdown string. Returns a JSON object with `additions`, `deletions`, `totalChanges`, and the full unified `diff`.

## Supported URL formats

- `/wiki/spaces/SPACEKEY/pages/123456789/Page+Title`
- `/wiki/pages/viewpage.action?pageId=123456789`

## Workflow: Recursively reading a page tree

1. Run `fetch-page` on the root URL.
2. Note the child pages listed at the bottom.
3. Run `fetch-page` for each child.
4. Repeat until all desired pages are fetched.

## Workflow: Syncing local docs

1. Read the local markdown file.
2. Run `compare` with the Confluence URL and the local file path.
3. If `totalChanges > 0`, present the diff to the user.

## Edge cases

- Empty pages return an empty markdown body — normal for container pages.
- If an attachment filename doesn't match, the script prints available attachments.
- 401/403 errors mean invalid or under-permissioned credentials.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/satiyap) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
