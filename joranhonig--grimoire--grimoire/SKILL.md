---
name: librarian-library-search
description: >- Use when this capability is needed.
metadata:
  author: JoranHonig
---

# Librarian Library Search

Perform semantic search across indexed library content via a local Qdrant
vector database.

## When to Use

This skill is the librarian agent's primary method for consulting local
knowledge bases. Use it **before** falling back to grep-based file search.

Semantic search finds relevant content even when exact keywords don't match —
a query for "flash loan price manipulation" will surface content about oracle
attacks and sandwich attacks that grep would miss.

## How to Run

The search script is at `skills/librarian-library-search/scripts/search.py`
inside the plugin directory.

```bash
uv run /path/to/grimoire/skills/librarian-library-search/scripts/search.py \
    "your natural language query here"
```

### Flags

| Flag | Default | Description |
|------|---------|-------------|
| `--limit` | `5` | Maximum number of results |
| `--library` | *(all)* | Filter to a specific library name |

### Examples

```bash
# Broad search across all libraries
uv run .../search.py "reentrancy vulnerability in pull-payment pattern"

# Scoped to a single library
uv run .../search.py "access control bypass" --library smart-contract-vulnerabilities

# More results
uv run .../search.py "ERC-4626 share inflation" --limit 10
```

## Output Format

The script prints a JSON array to stdout. Each element has:

```json
{
  "score": 0.82,
  "content": "[library-name] path/to/file\n\n...chunk text...",
  "metadata": {
    "library": "smart-contract-vulnerabilities",
    "file": "vulnerabilities/reentrancy.md",
    "chunk_idx": 2,
    "source_url": "git@github.com:kadenzipfel/smart-contract-vulnerabilities.git"
  }
}
```

- **score** — cosine similarity (0–1). Results above 0.6 are typically relevant.
- **content** — the chunk text, prefixed with library name and file path.
- **metadata.file** and **metadata.source_url** — use these to construct
  navigable GitHub URLs for citations.

## Handling Failures

- **"Qdrant database not found"** — the index hasn't been built. Tell the
  user to run `librarian-index` and fall back to grep for now.
- **"Collection not found"** — same as above; the collection is created
  during indexing.
- **No results or all scores below 0.5** — the query may not match indexed
  content. Fall back to grep-based search of the library files.

## Guidelines

- **Limit to 1–2 calls per research question.** Reformulate the query if the
  first attempt returns poor results rather than making many calls.
- **Do not use for indexing.** This skill is read-only. Use `librarian-index`
  to build or rebuild the index.
- **Same embedding model required.** The search script must use the same
  FastEmbed model that was used during indexing (default:
  `sentence-transformers/all-MiniLM-L6-v2`). Do not change `--embedding_model`
  unless you also re-indexed with that model.

---
> Source: [JoranHonig/grimoire](https://github.com/JoranHonig/grimoire) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-17 -->
