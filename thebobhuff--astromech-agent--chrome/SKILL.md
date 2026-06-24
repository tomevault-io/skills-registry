---
name: google-chrome
description: Use a headless Chrome browser to read websites or take screenshots. Use when this capability is needed.
metadata:
  author: thebobhuff
---

# Google Chrome (Headless)

Allows the agent to browse the live internet, read documentation, or verify UI via screenshots.

## Usage

Use the `browser.py` script to interact with web pages.

### Reading a Page (Default)

Converts the page content to Markdown for easy reading.

```bash
python app/skills/chrome/scripts/browser.py "https://example.com"
```

### Taking a Screenshot

Useful for visual verification.

```bash
python app/skills/chrome/scripts/browser.py "https://example.com" --action screenshot --output "example.png"
```

### Getting Raw Text

Useful for simple data extraction without formatting.

```bash
python app/skills/chrome/scripts/browser.py "https://example.com" --action text
```

## When to use

- When you need to read documentation that is not in the knowledge base.
- When you need to search for current information (combine with `web_search` tool to find URLs first).
- When you need to verify if a deployed website is working correctly.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/thebobhuff) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
