---
name: consume-feed
description: > Use when this capability is needed.
metadata:
  author: grahama1970
---

> STOP. READ THIS ENTIRE SKILL.MD BEFORE CALLING ANY ENDPOINT.

# Consume Feed Skill

A robust ingestion engine for upstream data sources.

## Phase 1: RSS (Implemented)

- "Pull the feeds now" -> `./run.sh run --mode manual`
- "Add this RSS feed <url>" -> `./run.sh sources add rss --url <url>`
- "Check feed ingest health" -> `./run.sh doctor`

## Phase 2: GitHub & NVD (Aspirational)

- "Add GitHub repo <owner>/<repo>" -> `sources add github --repo <owner>/<repo>`
- "Track NVD for <keyword>" -> `sources add nvd --query <keyword>`

## Usage

### Run Ingestion

```bash
# Run nightly crawl (all sources, respect intervals)
./run.sh run --mode nightly

# Run specific source immediately
./run.sh run --source <key>
```

### Manage Sources

```bash
# List all
./run.sh sources list

# Add RSS
./run.sh sources add rss --url "https://github.blog/feed/"
```

### Diagnosis & Initialization

```bash
# Health check
./run.sh doctor

# Force initialize search views and indexes
./run.sh doctor --init
```

## Resilience

- Uses **exponential backoff** and **jitter** for all network requests.
- Persists **checkpoints** (ETags, Timestamps) to resume efficiently.
- Reuses **Memory skill connection** for stable, shared database access.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/grahama1970) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
