---
name: fetch-spa
description: Fetches rendered content from JavaScript-heavy Single Page Applications (SPAs) like X/Twitter, React apps, etc. Use this when WebFetch fails to retrieve content from a JS-rendered page. Use when this capability is needed.
metadata:
  author: kasacchiful
---

# SPA Content Fetcher

You are a skill that fetches and analyzes content from JavaScript-heavy Single Page Applications (SPAs) that cannot be scraped with simple HTTP requests.

## Prerequisites

This skill requires `uv` to be installed. All Python dependencies (including `shot-scraper`) are managed automatically via PEP 723 inline script metadata — no manual `pip install` is needed.

## How to Use

When the user provides a URL to a JavaScript-heavy site (e.g., X/Twitter, React/Vue/Angular apps):

1. **Run the fetch script** to get the rendered text content:

```bash
uv run "${CLAUDE_PLUGIN_ROOT}/skills/fetch-spa/scripts/fetch_spa.py" "$URL"
```

### Options

- `--wait <ms>`: Wait time for JS rendering (default: 3000ms). Increase for slow-loading pages.
- `--selector <css>`: CSS selector to extract a specific element (e.g., `article`, `[data-testid="tweetText"]`)
- `--auth <path>`: Path to an auth.json file for authenticated sites.

### Site-Specific Selectors

For known sites, use these optimized selectors:

- **X/Twitter posts**: `--selector "[data-testid='tweetText']"` with `--wait 5000`
- **X/Twitter full thread**: `--selector "article"` with `--wait 5000`

### Examples

```bash
# Basic fetch
uv run "${CLAUDE_PLUGIN_ROOT}/skills/fetch-spa/scripts/fetch_spa.py" "https://example.com"

# X/Twitter post with optimized selector
uv run "${CLAUDE_PLUGIN_ROOT}/skills/fetch-spa/scripts/fetch_spa.py" "https://x.com/user/status/123" --selector "article" --wait 5000

# Authenticated site
uv run "${CLAUDE_PLUGIN_ROOT}/skills/fetch-spa/scripts/fetch_spa.py" "https://example.com/dashboard" --auth ~/auth.json
```

### Authentication Setup

For sites that require login, first create an auth session:

```bash
uv run --with shot-scraper shot-scraper auth "https://x.com/login" ~/x-auth.json
```

This opens a browser window where the user can log in manually. After login, press Enter to save the session.

2. **Analyze and present** the extracted content to the user in a clear, summarized format.

3. If the script fails or returns empty content:
   - Try increasing `--wait` time (e.g., 5000 or 10000)
   - Try a different `--selector`
   - Suggest the user set up authentication if the page requires login
   - As a fallback, try `uv run --with shot-scraper shot-scraper html "$URL" --wait 5000` to get full HTML and extract content from it

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kasacchiful) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
