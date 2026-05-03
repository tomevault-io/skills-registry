---
name: web-search
description: Search the web using DuckDuckGo (no API key required) Use when this capability is needed.
metadata:
  author: seshuthota
---

# Web Search Skill

Search the web for information using DuckDuckGo. No API key required.

## Usage

```bash
python3 {baseDir}/scripts/search.py "your search query"
python3 {baseDir}/scripts/search.py "your search query" --max_results 10
```

## Parameters

- `query`: The search query (required, first positional argument)
- `--max_results`: Maximum number of results to return (default: 5)

## Example

```bash
# Search for Python tutorials
python3 {baseDir}/scripts/search.py "python asyncio tutorial" --max_results 3
```

## Output

Returns JSON array with search results:
```json
[
  {
    "title": "Result title",
    "url": "https://example.com/...",
    "snippet": "Brief description..."
  }
]
```

## Notes

- Uses DuckDuckGo's HTML interface (rate-limited, use sparingly)
- For heavy usage, consider using the `brave-search` skill with API key

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/seshuthota) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
