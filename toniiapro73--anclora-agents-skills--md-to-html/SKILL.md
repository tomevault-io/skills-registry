---
name: md-to-html
description: This skill transforms Markdown documents into standalone, styled HTML files. It's designed to provide a clean, readable, and professional web representation of any Markdown content. Use when this capability is needed.
metadata:
  author: toniiapro73
---
---
name: md-to-html
description: Professional Markdown to HTML converter. Supports standard Markdown, tables, extensions, and applies a clean, modern CSS style for a premium look.
---

# Markdown to HTML Converter

## Purpose
This skill transforms Markdown documents into standalone, styled HTML files. It's designed to provide a clean, readable, and professional web representation of any Markdown content.

## Features
- **Accurate Parsing**: Uses the `markdown` Python library for robust conversion.
- **Premium Styling**: Automatically injects a modern, responsive CSS stylesheet (Inter font family, deep blue accents, elegant spacing).
- **Table Support**: Renders Markdown tables with professional styling.
- **Code Highlighting**: Basic styling for code blocks.
- **Standalone HTML**: Produces single-file outputs with embedded styles.

## Usage
Run the script providing the input file:

```bash
python skills/md-to-html/scripts/convert_md_to_html.py <input.md> [output.html]
```

## Requirements
- `markdown` (Python library)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/toniiapro73) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
