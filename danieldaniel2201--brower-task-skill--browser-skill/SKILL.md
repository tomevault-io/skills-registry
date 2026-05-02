---
name: browser-task
description: Orchestrates browser tasks requiring multi-tool coordination: search-then-navigate, login-then-download, multi-page interactions. Use when the agent needs to: (1) Search for a website then interact with it, (2) Login or fill forms, (3) Navigate multiple pages to find a target, (4) Extract download links from dynamic pages. For simple cases: use webfetch for static pages, curl for known file URLs. Use when this capability is needed.
metadata:
  author: danieldaniel2201
---

# BrowserTask

Complete complex browser tasks requiring multi-step, multi-tool coordination.

## Available Tools

| Tool | Capability | Use When |
|------|------------|----------|
| Search tools | Find trusted URLs | Target website unknown |
| webfetch | Fetch page content/HTML | Static pages, parse links |
| agent-browser | Browser interaction | Click, fill forms, login, dynamic content |
| curl | Download files | File URL is known |

## Core Principles

1. **Search first, never guess URLs** - Use search tools to get trusted URLs
2. **Right tool for the job** - agent-browser for interaction, curl for download
3. **Extract link then download** - agent-browser cannot download files directly; extract href then use curl

## Standard Download Pattern

```
Search/known URL -> agent-browser navigate to download page -> get download button href -> curl download
```

Key commands:
```bash
# Get download button's link
agent-browser get attr @download-button href

# Or use webfetch to get HTML and parse
webfetch <url> --format html
```

## agent-browser Key Points

For detailed commands, load agent-browser skill. Common usage:
- `agent-browser open <url>` - Open page
- `agent-browser snapshot -i` - Get interactive elements
- `agent-browser click @e1` - Click element
- `agent-browser get attr @e1 href` - Get link attribute

## Important Notes

- agent-browser is slow to start; use webfetch/curl when possible
- Search results are more reliable than guessed URLs
- Always use curl for file downloads, never attempt with agent-browser

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/danieldaniel2201) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
