---
name: firecrawl
description: Web scraping and URL discovery using Firecrawl. Use for scraping web pages to markdown and discovering URLs on websites. Keywords: scrape, crawl, firecrawl, web, markdown, url discovery, sitemap. Use when this capability is needed.
metadata:
  author: mandalorian007
---

# Firecrawl

Web scraping and URL discovery powered by Firecrawl API.

## Variables

- **FIRECRAWL_CLI_PATH**: `.claude/skills/firecrawl/firecrawl_cli/`

## Instructions

Run from FIRECRAWL_CLI_PATH:
```bash
cd .claude/skills/firecrawl/firecrawl_cli/
uv run fc --help                   # Discover all commands
uv run fc <command> --help         # Detailed usage
```

**Rules:**
- **ALWAYS use Task agent for scrapes** - pages are large and will overload context
- **Use map to explore** - when you need to find where content lives on a site
- **Use Perplexity for search** - this skill is for targeted scraping, not search

## Troubleshooting

- **"FIRECRAWL_API_KEY not found"**: Run `/prime` to validate environment

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mandalorian007) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
