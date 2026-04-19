---
name: crawl4ai
description: Web crawler that converts URLs to clean markdown. Use when user wants to fetch a webpage, extract web content, convert URL to markdown, or scrape website content. Use when this capability is needed.
metadata:
  author: alexjx
---

# Crawl4AI Web Crawler

Converts web pages to clean, LLM-friendly markdown using crawl4ai.

## Requirements

- `uv` must be installed (https://docs.astral.sh/uv/)
- The skill manages its own isolated Python environment via `uv sync`

## Setup

Before first use, run the setup script:

```bash
skills/crawl4ai/scripts/setup.sh
```

This will:
1. Check for `uv` installation
2. Run `uv sync` to create `.venv/` and install dependencies
3. Install Playwright Chromium browser (`python -m playwright install chromium --with-deps`)
4. Run `crawl4ai-doctor` to verify installation

## Usage

To crawl a URL and get markdown (from the skill directory):

```bash
cd skills/crawl4ai
uv run python scripts/crawl.py "https://example.com"
# Or after setup:
uv run crawl "https://example.com"
```

Options:
- `--output FILE` - Save markdown to file instead of stdout
- `--include-links` - Include hyperlinks in markdown output
- `--include-images` - Include image references
- `--no-headless` - Show browser window (default: headless)
- `--timeout SECONDS` - Page load timeout (default: 30)
- `--verbose` - Enable crawler progress logs

## Examples

### Basic crawl
```bash
cd skills/crawl4ai
uv run python scripts/crawl.py "https://docs.python.org/3/"
```

### Save to file
```bash
uv run python scripts/crawl.py "https://example.com" --output content.md
```

### With all options
```bash
uv run python scripts/crawl.py "https://example.com" \
  --include-links \
  --include-images \
  --timeout 60 \
  --output result.md
```

## Troubleshooting

If crawling fails:
1. Run `uv run crawl4ai-doctor` to diagnose
2. Ensure playwright browsers are installed: `uv run python -m playwright install chromium`
3. Check the URL is accessible and not blocked by robots.txt

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/alexjx) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
