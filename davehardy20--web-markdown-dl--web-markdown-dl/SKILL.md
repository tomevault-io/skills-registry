---
name: web-markdown-dl
description: Convert web pages to clean Markdown format. Use when you need to download web content, scrape articles, or convert URLs to markdown. Supports single URLs, batch processing from files, and website crawling with depth control. Triggers on 'convert url to markdown', 'download webpage', 'scrape website', 'batch convert urls', 'crawl website for content'. Use when this capability is needed.
metadata:
  author: davehardy20
---

# Web Markdown DL

Convert web pages to clean, structured Markdown format.

## Prerequisites

- Run the install script to build and install the skill: `./install-skill.sh`
- The binary will be installed to `~/.config/opencode/skills/web-markdown-dl/bin/`

## Capabilities

### 1. Convert Single URL

Convert a single web page to Markdown:

```bash
web-markdown-dl --url "https://example.com/article"
```

Options:
- `--output <file>` - Save to file instead of stdout
- `--format <markdown|json>` - Output format (default: markdown)
- `--timeout <ms>` - Request timeout (default: 30000)
- `--filter` - Enable content filtering (remove ads/nav/sidebars)
- `--user-agent <ua>` - Custom user agent string

### 2. Batch Processing

Process multiple URLs from a file:

```bash
web-markdown-dl --input-file urls.txt --output-dir ./output
```

Input file format (one URL per line):
```
https://example.com/page1
https://example.com/page2
https://example.com/page3
```

Options:
- `--delay <ms>` - Delay between requests (default: 1000)
- All single URL options apply

### 3. Website Crawling

Crawl a website recursively:

```bash
web-markdown-dl --url "https://example.com" --crawl --output-dir ./crawled
```

Options:
- `--max-depth <n>` - Maximum crawl depth (default: 2)
- `--limit <n>` - Maximum URLs to crawl (default: 100)
- `--ignore-robots` - Ignore robots.txt restrictions
- `--allow-external` - Allow crawling external domains

### 4. Wrapper Script

Use the wrapper script for simplified commands:

```bash
# Convert URL to markdown
~/.config/opencode/skills/web-markdown-dl/scripts/run.sh convert --url "https://example.com"

# Batch process
~/.config/opencode/skills/web-markdown-dl/scripts/run.sh batch --input-file urls.txt --output-dir ./output

# Crawl website
~/.config/opencode/skills/web-markdown-dl/scripts/run.sh crawl --url "https://example.com" --output-dir ./crawled --max-depth 3
```

## Output Format

### Markdown Output

Returns clean GitHub Flavored Markdown:
- Proper heading hierarchy
- Code blocks with syntax highlighting
- Tables, lists, and links preserved
- Images with alt text

### JSON Output

With `--format json`, returns structured data:

```json
{
  "markdown": "# Title\n\nContent...",
  "metadata": {
    "title": "Page Title",
    "url": "https://example.com",
    "author": "Author Name",
    "publishedDate": "2024-01-15",
    "wordCount": 1500
  }
}
```

## Common Use Cases

### Extract Article Content

```bash
web-markdown-dl --url "https://blog.example.com/post" --filter
```

### Batch Download Documentation

```bash
# Create urls.txt with doc URLs
web-markdown-dl --input-file urls.txt --output-dir ./docs --delay 500
```

### Crawl API Reference

```bash
web-markdown-dl --url "https://api.example.com/docs" --crawl --output-dir ./api-docs --max-depth 3
```

### Get Structured Data

```bash
web-markdown-dl --url "https://example.com" --format json --output page.json
```

## Error Handling

The CLI returns appropriate exit codes:
- `0` - Success
- `1` - Error (invalid URL, network failure, etc.)

Errors are written to stderr, content to stdout.

## Notes

- Uses headless browser (Playwright) for JavaScript-rendered pages
- Respects robots.txt by default (use `--ignore-robots` to override)
- Stays within original domain by default (use `--allow-external` to crawl external links)
- Run `./install-skill.sh` to (re)install the skill after pulling updates

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/davehardy20) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
