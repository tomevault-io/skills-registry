---
name: curated-search
description: Domain-restricted full-text search over curated technical documentation Use when this capability is needed.
metadata:
  author: openclaw
---

# Curated Search Skill

## Summary
Domain-restricted full-text search over a curated whitelist of technical documentation (MDN, Python docs, etc.). Provides clean, authoritative results without web spam.

## External Endpoints

This skill does **not** call any external network endpoints during search operations. The crawler optionally makes outbound HTTP requests during index builds (one‑time setup), but those are user‑initiated (`npm run crawl`) and respect the configured domain whitelist.

## Security & Privacy

- **Search is fully local** – After the index is built, all queries run offline; no data leaves your machine.
- **Crawling is optional and whitelist‑scoped** – The crawler only accesses domains you explicitly list in `config.yaml`. It respects `robots.txt` and configurable delays.
- **No telemetry** – No usage data is transmitted externally.
- **Configuration** is read from local `config.yaml` and the index file in `data/`.

## Model Invocation Note

The `curated-search.search` tool is invoked **only when the user explicitly calls it**. It does not run autonomously. OpenClaw calls the tool handler (`scripts/search.js`) when the user asks to search the curated index.

## Trust Statement

By using this skill, you trust that the code operates locally and only crawls domains you approve. The skill does not send your queries or workspace data to any third party. Review the open‑source implementation before installing.

---

## Tool: curated-search.search

Search the curated index.

### Parameters

| Name | Type | Required | Default | Description |
|------|------|----------|---------|-------------|
| `query` | string | yes | — | Search query terms |
| `limit` | number | no | 5 | Maximum results (capped by config.max_limit, typically 100) |
| `domain` | string | no | null | Filter to specific domain (e.g., `docs.python.org`) |
| `min_score` | number | no | 0.0 | Minimum relevance score (0.0–1.0); filters out low-quality matches |
| `offset` | number | no | 0 | Pagination offset (skip first N results) |

### Response

JSON array of result objects:

```json
[
  {
    "title": "Python Tutorial",
    "url": "https://docs.python.org/3/tutorial/",
    "snippet": "Python is an easy to learn, powerful programming language...",
    "domain": "docs.python.org",
    "score": 0.87,
    "crawled_at": 1707712345678
  }
]
```

**Fields:**
- `title` — Document title (cleaned)
- `url` — Source URL (canonical)
- `snippet` — Excerpt (~200 chars) from content
- `domain` — Hostname of source
- `score` — BM25 relevance score (higher is better; not normalized 0–1 but typically 0–1 range)
- `crawled_at` — Unix timestamp when page was crawled

### Example Agent Calls

```
search CuratedSearch for "python tutorial"
search CuratedSearch for "async await" limit=3 domain=developer.mozilla.org
search CuratedSearch for "linux man page" min_score=0.3
```

### Errors

If an error occurs, the tool exits non-zero and prints a JSON error object to stderr, e.g.:

```json
{
  "error": "index_not_found",
  "message": "Search index not found. The index has not been built yet.",
  "suggestion": "Run the crawler first: npm run crawl",
  "details": { "path": "data/index.json" }
}
```

Common error codes:

| Code | Meaning | Suggested Fix |
|------|---------|---------------|
| `config_missing` | Configuration file not found | Specify `--config` path or ensure config.yaml exists |
| `config_invalid` | YAML parsing failed | Check syntax in config.yaml |
| `config_missing_index_path` | `index.path` not set | Add index.path to config |
| `index_not_found` | Index file missing | Run `npm run crawl` to build index |
| `index_corrupted` | Index file incompatible or corrupted | Rebuild index with `npm run crawl` |
| `index_init_failed` | Unexpected index initialization error | Check permissions, reinstall dependencies |
| `missing_query` | No query provided | Provide `--query` argument |
| `query_too_long` | Query exceeds 1000 characters | Shorten the query |
| `limit_exceeded` | Limit > config.max_limit | Use a smaller limit |
| `invalid_domain` | Domain filter malformed | Use format like `docs.python.org` |
| `conflicting_flags` | Mutually exclusive flags used (e.g., `--stats` with `--query`) | Use flags correctly |
| `stats_failed` | Could not retrieve index stats | Ensure index is accessible |
| `search_failed` | Search execution threw an error | Check query and index integrity |

## Configuration

Edit `config.yaml` in the skill directory. Key sections:

- `domains` — whitelist of allowed domains (required)
- `seeds` — starting URLs for crawling
- `crawl` — depth, delay, timeout, max_documents
- `content` — min_content_length, max_content_length
- `index` — path to index files
- `search` — default_limit, max_limit, min_score

See `README.md` for full configuration docs.

## Support

- Full documentation: `README.md`
- Technical specs: `specs/`
- Build plan: `PLAN.md`
- Contributor guide: `CONTRIBUTING.md`
- Issues: Report on GitHub (or via OpenClaw maintainers)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
