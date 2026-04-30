---
name: enact-playwright
description: Optional CSS selector to target specific element Use when this capability is needed.
metadata:
  author: aiskillstore
---

# Playwright Browser Automation

A browser automation tool that uses Playwright to interact with web pages.

## Features

- Navigate to any URL
- Take screenshots
- Extract text content
- Extract HTML content
- Target specific elements with CSS selectors

## Usage

```bash
# Get text content from a page
enact run enact/playwright --args '{"url": "https://example.com"}'

# Take a screenshot
enact run enact/playwright --args '{"url": "https://example.com", "action": "screenshot"}'

# Extract text from a specific element
enact run enact/playwright --args '{"url": "https://example.com", "selector": "h1"}'
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/aiskillstore) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
