---
name: reddit-scraper
description: Tools for fetching and displaying the latest posts from subreddits. Use this skill when the user wants to see recent activity from specific Reddit communities. Use when this capability is needed.
metadata:
  author: leonardoteodoroo
---

# Reddit Scraper

## Overview

This skill allows you to fetch and display the latest posts from any public subreddit. It uses the Reddit JSON API (adding `.json` to subreddit URLs) to retrieve data without robust authentication headers, making it lightweight for simple retrieval tasks.

## Capabilities

1.  **Fetch Latest Posts**: Retrieve the top 3 newest posts (title, author, score, URL, content preview).
2.  **CLI Chat Interface**: A simple interactive loop to query multiple subreddits in a row.

## Usage

### 1. Interactive Chat Mode

To start an interactive session where you can query subreddits one by one:

```bash
python3 .agent/skills/reddit-scraper/scripts/chat_interface.py
```

### 2. Direct Query (Single Subreddit)

To fetch the latest posts for a specific subreddit (e.g., `n8n`) directly:

```bash
python3 .agent/skills/reddit-scraper/scripts/fetch_posts.py Singularity --sort top --time week --limit 3
```

Arguments:

- `subreddit`: Name of the subreddit.
- `--sort`: Sort order (new, hot, top, rising, controversial). Default: new.
- `--time`: Time filter (hour, day, week, month, year, all). Default: all.
- `--limit`: Number of results. Default: 3.

### 3. Advanced Search (Filtered by Query)

To search for posts within a subreddit with specific filters (query, time, sort):

```bash
python3 .agent/skills/reddit-scraper/scripts/fetch_filtered_posts.py Singularity "women's fashion" --sort top --time week --limit 3
```

Arguments:

- `subreddit`: Name of the subreddit.
- `query`: Search term.
- `--sort`: Sort order (relevance, hot, top, new, comments). Default: top.
- `--time`: Time filter (hour, day, week, month, year, all). Default: week.
- `--limit`: Number of results. Default: 3.

## Dependencies

This skill requires the `requests` library.
Check if it's installed:

```bash
pip list | grep requests
```

Install if missing:

```bash
pip install requests
```

## Implementation Details

The scripts are located in `scripts/`:

- `fetch_posts.py`: Contains the `get_latest_posts` function and CLI entry point.
- `fetch_filtered_posts.py`: Helper script for searching posts with filters (query, time, sort).
- `chat_interface.py`: Imports `fetch_posts` and runs the input loop.

**Note:** The scripts use a custom User-Agent to avoid immediate 429 (Too Many Requests) errors from Reddit, but heavy usage might still trigger rate limits.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/leonardoteodoroo) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
