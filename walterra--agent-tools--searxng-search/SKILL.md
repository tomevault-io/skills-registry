---
name: searxng-search
description: Web search and content extraction via SearXNG metasearch engine. Use when you need to search the web, look up documentation, research APIs, find current information, or extract content from URLs. No API keys required, no rate limits, privacy-preserving. Use when this capability is needed.
metadata:
  author: walterra
---

# SearXNG Search

Local web search using SearXNG metasearch engine. No external API keys required.

## Prerequisites

- **SearXNG instance** running locally (default: `http://localhost:8080`) or accessible via network
- **Node.js 18+** installed

### Quick SearXNG Setup

The JSON API must be enabled in SearXNG settings (it's disabled by default).
This skill includes a pre-configured `settings.yml` that enables it.

```bash
# Using Docker (recommended) - mount the included settings to enable JSON API
docker run -d -p 8080:8080 --name searxng \
  -v {baseDir}/searxng/settings.yml:/etc/searxng/settings.yml:ro \
  searxng/searxng

# Or see https://docs.searxng.org/admin/installation.html
```

> **Note:** The default SearXNG image only enables HTML format. Without the
> custom settings, the JSON API will return HTTP 403 Forbidden.

## Setup

Before first use, install dependencies:

```bash
cd {baseDir} && npm install
```

## Environment

The skill auto-detects whether it's running locally or in Docker:

| Environment | Default URL | How detected |
|-------------|-------------|--------------|
| Local | `http://localhost:8080` | No `/.dockerenv` file |
| Docker | `http://searxng:8080` | `/.dockerenv` exists |

Override with `SEARXNG_URL` environment variable if needed.

## Search

```bash
{baseDir}/scripts/search.js "query"                    # Basic search (5 results)
{baseDir}/scripts/search.js "query" -n 10              # More results
{baseDir}/scripts/search.js "query" --content          # Include page content as markdown
{baseDir}/scripts/search.js "query" -n 3 --content     # Combined
```

### Examples

```bash
# Search for documentation
{baseDir}/scripts/search.js "typescript generics tutorial"

# Research with content extraction
{baseDir}/scripts/search.js "react server components" -n 5 --content

# Find API references
{baseDir}/scripts/search.js "site:docs.github.com REST API" -n 3
```

## Extract Page Content

Fetch a URL and extract readable content as markdown:

```bash
{baseDir}/scripts/content.js https://example.com/article
```

### Examples

```bash
# Extract documentation page
{baseDir}/scripts/content.js https://docs.python.org/3/tutorial/classes.html

# Extract blog post
{baseDir}/scripts/content.js https://blog.example.com/some-article
```

## Output Format

### Search Results

```
--- Result 1 ---
Title: Page Title
Link: https://example.com/page
Snippet: Description from search results
Content: (if --content flag used)
  Markdown content extracted from the page...

--- Result 2 ---
...
```

### Content Extraction

```
Title: Article Title
URL: https://example.com/article

--- Content ---
Markdown content of the page...
```

## Advantages

- ✅ No API keys required
- ✅ No rate limits
- ✅ Privacy-preserving (local instance)
- ✅ Aggregates results from multiple search engines
- ✅ Fast and reliable
- ✅ Works locally and in Docker containers

## When to Use

- Searching for documentation or API references
- Looking up facts or current information
- Researching libraries, frameworks, or tools
- Fetching content from specific URLs
- Any task requiring web search without external dependencies

## Troubleshooting

### Connection refused

SearXNG is not running. Start it with:
```bash
docker run -d -p 8080:8080 --name searxng \
  -v {baseDir}/searxng/settings.yml:/etc/searxng/settings.yml:ro \
  searxng/searxng
```

### HTTP 403 Forbidden

The JSON API is not enabled. The default SearXNG only allows HTML format.
Ensure you mount the included `settings.yml` when starting the container:
```bash
docker stop searxng && docker rm searxng
docker run -d -p 8080:8080 --name searxng \
  -v {baseDir}/searxng/settings.yml:/etc/searxng/settings.yml:ro \
  searxng/searxng
```

### Custom SearXNG URL

Set the environment variable:
```bash
export SEARXNG_URL=http://your-searxng-host:8080
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/walterra) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-12 -->
