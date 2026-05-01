---
name: brave-headless
description:
  Headless web search and content extraction via the Brave Search API.
  Features exponential-backoff retry, circuit breaker fault isolation, bounded-concurrency
  parallel page fetching, structured leveled logging, and smart paragraph-boundary truncation.
  No browser required. Use for web research, documentation lookup, URL content extraction,
  and any workflow requiring scriptable, non-interactive web search.
version: 0.2.0
license: MIT
compatibility: Requires Node.js >= 20 and npm. Works on macOS and Linux.
metadata:
  author: kelexine
  homepage: https://github.com/kelexine/brave-headless
  openclaw:
    requires:
      env:
        - BRAVE_API_KEY
      bins:
        - node
        - npm
    primaryEnv: BRAVE_API_KEY
    emoji: "🔍"
    homepage: https://github.com/kelexine/brave-headless
    os:
      - macos
      - linux
    install:
      - kind: node
        package: "@mozilla/readability"
        bins: []
      - kind: node
        package: jsdom
        bins: []
      - kind: node
        package: turndown
        bins: []
      - kind: node
        package: turndown-plugin-gfm
        bins: []
---

# brave-search

Headless web search and content extraction via the Brave Search API.

## Setup

Run once before first use:

```bash
cd <skill-root>
npm ci
```

Required environment variable:

```bash
export BRAVE_API_KEY="your-key-here"
```

Get a free API key at [brave.com/search/api](https://brave.com/search/api).

## Usage

### Search

```bash
node scripts/search.js "query"                        # Basic (5 results)
node scripts/search.js "query" -n 10                  # Up to 20 results
node scripts/search.js "query" --content              # Include page content
node scripts/search.js "query" -n 3 --content         # Combined
node scripts/search.js "query" --json                 # Newline-delimited JSON
node scripts/search.js --help                         # Full options + env vars
```

### Extract page content

```bash
node scripts/content.js https://example.com/article
node scripts/content.js https://example.com/article --json
node scripts/content.js https://example.com/article --max-length 8000
```

## Output format (plain text)

```
--- Result 1 ---
Title:   Page Title
URL:     https://example.com/page
Snippet: Description from Brave Search
Content:
  # Page Title

  Extracted markdown content...

--- Result 2 ---
...
```

Pass `--json` to get one JSON object per line instead, suitable for piping.

## Exit codes

| Code | Meaning                                          |
|------|--------------------------------------------------|
| `0`  | Success                                          |
| `1`  | Invalid input or configuration error             |
| `2`  | Page had no extractable content (`content.js`)   |
| `130`| Interrupted (SIGINT)                             |

## Configuration (environment variables)

All behaviour is configurable without touching code:

| Variable               | Default  | Description                                    |
|------------------------|----------|------------------------------------------------|
| `BRAVE_API_KEY`        | —        | **Required.** Brave Search subscription token  |
| `LOG_LEVEL`            | `info`   | `debug` · `info` · `warn` · `error` · `silent` |
| `LOG_JSON`             | `false`  | Emit logs as newline-delimited JSON to stderr  |
| `FETCH_TIMEOUT_MS`     | `15000`  | Per-page fetch timeout                         |
| `SEARCH_TIMEOUT_MS`    | `10000`  | Brave API call timeout                         |
| `MAX_CONTENT_LENGTH`   | `5000`   | Max chars of extracted content                 |
| `MAX_RETRY_ATTEMPTS`   | `3`      | Retry attempts on transient errors             |
| `RETRY_BASE_DELAY_MS`  | `500`    | Base delay for exponential backoff             |
| `RETRY_MAX_DELAY_MS`   | `30000`  | Backoff delay cap                              |
| `CONCURRENCY_LIMIT`    | `3`      | Parallel page fetches when `--content` is set  |
| `CB_FAILURE_THRESHOLD` | `5`      | Consecutive failures before circuit opens      |
| `CB_RESET_TIMEOUT_MS`  | `60000`  | Circuit breaker reset window                   |

All variables are validated at startup — misconfigured runs fail immediately with a descriptive
list of every bad value rather than crashing mid-execution.

## Architecture

See [`references/ARCHITECTURE.md`](references/ARCHITECTURE.md) for a full module breakdown.

```
scripts/
├── search.js            ← Search CLI entry point
├── content.js           ← Content extraction CLI entry point
├── content-fetcher.js   ← HTTP fetch + Readability + DOM fallback
├── config.js            ← Schema-validated env config
├── circuit-breaker.js   ← Fault isolation (CLOSED → OPEN → HALF_OPEN)
├── retry.js             ← Exponential backoff with full jitter
├── concurrency.js       ← Bounded parallel execution pool
├── utils.js             ← htmlToMarkdown, smartTruncate, parseURL
├── logger.js            ← Structured leveled logger → stderr
└── errors.js            ← Typed error hierarchy
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
