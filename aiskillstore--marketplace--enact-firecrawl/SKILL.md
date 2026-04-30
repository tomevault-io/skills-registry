---
name: enact-firecrawl
description: Extract structured data from a page using AI Use when this capability is needed.
metadata:
  author: aiskillstore
---

# Firecrawl Web Scraping Tool

A powerful web scraping tool that uses the [Firecrawl API](https://firecrawl.dev) to convert websites into clean, LLM-ready markdown and extract structured data.

## Features

- **Scrape**: Extract content from a single URL as markdown, HTML, or with screenshots
- **Crawl**: Automatically discover and scrape all accessible subpages of a website
- **Map**: Get a list of all URLs from a website without scraping content (extremely fast)
- **Search**: Search the web and get full scraped content from results
- **Extract**: Use AI to extract structured data from pages with natural language prompts

## Setup

1. Get an API key from [firecrawl.dev](https://firecrawl.dev)
2. Set your API key as a secret:
   ```bash
   enact env set FIRECRAWL_API_KEY <your-api-key> --secret --namespace enact
   ```

This stores your API key securely in your OS keyring (macOS Keychain, Windows Credential Manager, or Linux Secret Service).

## Usage Examples

### CLI

#### Scrape a single page
```bash
enact run enact/firecrawl -a '{"url": "https://example.com", "action": "scrape"}'
```

#### Crawl an entire documentation site
```bash
enact run enact/firecrawl -a '{"url": "https://docs.example.com", "action": "crawl", "limit": 20}'
```

#### Map all URLs on a website
```bash
enact run enact/firecrawl -a '{"url": "https://example.com", "action": "map"}'
```

#### Search the web
```bash
enact run enact/firecrawl -a '{"url": "latest AI developments 2024", "action": "search", "limit": 5}'
```

#### Extract structured data with AI
```bash
enact run enact/firecrawl -a '{"url": "https://news.ycombinator.com", "action": "extract", "prompt": "Extract the top 10 news headlines with their URLs"}'
```

#### Extract with a JSON schema
```bash
enact run enact/firecrawl -a '{
  "url": "https://example.com/pricing",
  "action": "extract",
  "prompt": "Extract pricing information",
  "schema": "{\"type\":\"object\",\"properties\":{\"plans\":{\"type\":\"array\",\"items\":{\"type\":\"object\",\"properties\":{\"name\":{\"type\":\"string\"},\"price\":{\"type\":\"string\"}}}}}}"
}'
```

### MCP (for LLMs/Agents)

When using this tool via MCP, call `enact__firecrawl` with these parameters:

#### Scrape a single page
Call with `url` set to the target URL and `action` set to `"scrape"`.

#### Crawl a documentation site
Call with `url`, `action` set to `"crawl"`, and `limit` to control the maximum number of pages.

#### Map all URLs on a website
Call with `url` and `action` set to `"map"` to discover all URLs without scraping content.

#### Search the web
Call with `url` set to your search query (e.g., "latest AI news") and `action` set to `"search"`. Use `limit` to control result count.

#### Extract structured data with AI
Call with `url`, `action` set to `"extract"`, and `prompt` describing what data to extract. Optionally provide a `schema` for structured output.

## Output

The tool returns JSON with:
- **markdown**: Clean, LLM-ready content
- **metadata**: Title, description, language, source URL
- **extract**: Structured data (for extract action)
- **links**: Discovered URLs (for map action)

## API Features

Firecrawl handles the hard parts of web scraping:
- Anti-bot mechanisms
- Dynamic JavaScript content
- Proxies and rate limiting
- PDF and document parsing
- Screenshot capture

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/aiskillstore) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
