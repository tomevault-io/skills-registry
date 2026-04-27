---
name: ingest-book
description: > Use when this capability is needed.
metadata:
  author: grahama1970
---

> STOP. READ THIS ENTIRE SKILL.MD BEFORE CALLING ANY ENDPOINT.

# ingest-book

**Manage Readarr library, search for books, and monitor downloads.**

## Commands

- `multi-search <term>`: Search all configured Newznab indexers in parallel, deduplicate results.
- `nzb-search <term>`: Direct Usenet search via NZBGeek only.
- `advanced-search --title/--author/--isbn`: GeekSeek-syntax search.
- `list-indexers`: Show configured indexers (keys masked).
- `search <term>`: Search for books/authors in Readarr.
- `add <term>`: Search and add the first matching book (Auto-Learn).
- `health`: Check if Readarr is running and healthy.
- `ensure-running`: Start Readarr if not running.

## Usage

```bash
# Multi-indexer search (recommended for technical books)
./run.sh multi-search "High Performance Python"

# Single-indexer NZBGeek search
./run.sh nzb-search "The Art of Exploitation"

# Check configured indexers
./run.sh list-indexers

# Readarr search
./run.sh search "The Art of Exploitation"
```

## Multi-Indexer Configuration

For best technical book coverage, configure multiple Newznab indexers.

### Option 1: JSON config file (`~/.config/ingest-book/indexers.json`)

```json
[
  {"name": "NZBGeek", "api_url": "https://api.nzbgeek.info/api", "api_key": "your-key"},
  {"name": "DrunkenSlug", "api_url": "https://api.drunkenslug.com/api", "api_key": "your-key"},
  {"name": "Althub", "api_url": "https://api.althub.co.za/api", "api_key": "your-key"}
]
```

### Option 2: `NEWZNAB_INDEXERS` env var (same JSON format)

### Option 3: Legacy single-indexer (NZBGeek only)

Set `NZBD_GEEK_API_KEY` or `NZBGEEK_API_KEY` in `.env`.

## Readarr Configuration

- **Readarr Path**: `~/workspace/experiments/Readarr/Readarr`
- **Data Path**: `~/workspace/experiments/Readarr/data`
- **Port**: 8787
- **API Key**: `READARR_API_KEY` env var (optional for localhost in some configs)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/grahama1970) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
