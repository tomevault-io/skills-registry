---
name: browser-tools
description: Browser automation via Chrome DevTools Protocol. Use for web scraping, page inspection, screenshots, DOM interaction, Google search, and content extraction. Start Chrome, navigate, evaluate JavaScript, take screenshots, pick elements, get cookies, search Google, and extract page content as markdown. Use when this capability is needed.
metadata:
  author: devular
---

# Browser Tools

Chrome DevTools Protocol tools for agent-assisted web automation. Scripts are in `~/agent-tools/browser-tools/` and must be invoked with full path or with that directory in PATH.

**Port**: Set `BROWSER_DEBUG_PORT` env var to override the default (9222). Example: `export BROWSER_DEBUG_PORT=9333`

## Setup

```bash
cd ~/agent-tools/browser-tools && npm install
```

## Start Chrome

```bash
~/agent-tools/browser-tools/browser-start.js              # Fresh profile
~/agent-tools/browser-tools/browser-start.js --profile    # Copy user's profile (cookies, logins)
```

Launch Chrome with remote debugging. Use `--profile` to preserve user's authentication state.

## Navigate

```bash
~/agent-tools/browser-tools/browser-nav.js https://example.com
~/agent-tools/browser-tools/browser-nav.js https://example.com --new
```

Navigate to URLs. Use `--new` flag to open in a new tab instead of reusing current tab.

## Evaluate JavaScript

```bash
~/agent-tools/browser-tools/browser-eval.js 'document.title'
~/agent-tools/browser-tools/browser-eval.js 'document.querySelectorAll("a").length'
```

Execute JavaScript in the active tab. Code runs in async context.

## Screenshot

```bash
~/agent-tools/browser-tools/browser-screenshot.js
```

Capture current viewport and return temporary file path.

## Pick Elements

```bash
~/agent-tools/browser-tools/browser-pick.js "Click the submit button"
```

Interactive element picker. User clicks to select, Cmd/Ctrl+Click for multi-select, Enter to finish. Returns element info (tag, id, class, text, html, parents).

## Cookies

```bash
~/agent-tools/browser-tools/browser-cookies.js
```

Display all cookies for the current tab.

## Search Google

```bash
~/agent-tools/browser-tools/browser-search.js "rust programming"
~/agent-tools/browser-tools/browser-search.js "climate change" -n 10
~/agent-tools/browser-tools/browser-search.js "machine learning" -n 3 --content
```

Options:
- `-n <num>` - Number of results (default: 5)
- `--content` - Fetch and extract readable content as markdown from each result

## Extract Page Content

```bash
~/agent-tools/browser-tools/browser-content.js https://example.com
```

Navigate to a URL and extract readable content as markdown.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/devular) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
