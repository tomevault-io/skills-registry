---
name: md-to-pdf
description: Convert markdown files to clean, formatted PDFs using reportlab Use when this capability is needed.
metadata:
  author: openclaw
---

# Markdown to PDF

Convert markdown documents to professional, clean PDFs with proper formatting.

## Usage

```bash
# Basic usage
uv run scripts/md-to-pdf.py input.md

# Specify output
uv run scripts/md-to-pdf.py input.md -o output.pdf
uv run scripts/md-to-pdf.py input.md --output my-report.pdf

# Verbose mode
uv run scripts/md-to-pdf.py input.md -v
```

## Features

- **Headers**: H1-H6 with hierarchical styling
- **Text formatting**: Bold, italic, inline code
- **Lists**: Bullet lists, numbered lists, task lists
- **Code blocks**: Syntax highlighting with background
- **Tables**: Full table support with headers
- **Links**: Clickable hyperlinks
- **Horizontal rules**: Visual section dividers
- **YAML frontmatter**: Automatically skipped
- **Special characters**: Emojis, Unicode symbols
- **Page numbers**: Automatic footer with page numbers
- **Professional styling**: Clean, readable output

## Options

- `-o, --output`: Output PDF file path (default: input_filename.pdf)
- `-v, --verbose`: Print detailed processing information

## Supported Markdown Elements

| Element | Syntax | Status |
|---------|--------|--------|
| Headers | `# H1` to `###### H6` | ✅ |
| Bold | `**text**` or `__text__` | ✅ |
| Italic | `*text*` or `_text_` | ✅ |
| Inline code | `` `code` `` | ✅ |
| Code blocks | ``` | ✅ |
| Bullet lists | `- item` or `* item` | ✅ |
| Numbered lists | `1. item` | ✅ |
| Task lists | `- [x] done` | ✅ |
| Tables | `| col | col |` | ✅ |
| Links | `[text](url)` | ✅ |
| Horizontal rules | `---` or `***` | ✅ |
| Blockquotes | `> quote` | ✅ |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
