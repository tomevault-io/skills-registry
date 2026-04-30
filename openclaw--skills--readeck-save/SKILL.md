---
name: readeck
description: Save articles to Readeck (self-hosted read-it-later app). Use when the user wants to save an article for later reading, add something to their reading list, or send a page to Readeck. Use when this capability is needed.
metadata:
  author: openclaw
---

# Readeck

Save articles to a self-hosted Readeck instance for later reading.

## Setup

Set these environment variables (in your shell profile or Clawdbot config):

```bash
export READECK_URL="https://your-readeck-instance.com"
export READECK_API_TOKEN="your-api-token"
```

To get your API token: Readeck → Settings → API tokens → Create new token.

## Save an article

```bash
{baseDir}/scripts/save.sh "<URL>"
```

Example:
```bash
{baseDir}/scripts/save.sh "https://example.com/interesting-article"
```

## API Details

- **Endpoint:** `POST {READECK_URL}/api/bookmarks`
- **Auth:** Bearer token
- **Body:** `{"url": "..."}`

## Response

Returns `{"status":202,"message":"Link submited"}` on success.
Readeck will fetch and process the article content automatically.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
