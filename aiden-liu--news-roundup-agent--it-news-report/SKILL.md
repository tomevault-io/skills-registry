---
name: it-news-report
description: Search for IT news on a topic, enrich each result with a web search, and generate a Markdown report saved in /workspace. Use this skill when the user asks for a news report or a roundup on a technology topic. Default search query is "small ai local models". Use when this capability is needed.
metadata:
  author: aiden-liu
---
# IT News Report

## Purpose

Generate a comprehensive Markdown news report on a given topic (default: "small ai local models").

## Steps to follow

### Step 1 — Search for recent news

Use the `brave-news-search` skill with the provided query (or the default if none given) to retrieve a list of recent news articles.

```bash
curl -s "https://api.search.brave.com/res/v1/news/search?q=$(echo "${ARGUMENTS_REST:-small ai local models}" | sed 's/ /+/g')&count=3&freshness=pw" \
  -H "X-Subscription-Token: ${BRAVE}" \
  -H "Accept: application/json"
```

### Step 2 — Enrich each article

For each article returned in Step 1, use the `brave-web-search` skill with the article URL to retrieve additional context and details.

```bash
curl -s "https://api.search.brave.com/res/v1/web/search?q=$(echo "$ARTICLE_URL" | sed 's/ /+/g')&count=10" \
  -H "X-Subscription-Token: ${BRAVE}" \
  -H "Accept: application/json"
```

### Step 3 — Generate the Markdown report

Using all the collected information, write a well-structured Markdown report saved to `/workspace/news-report.md`.

The report must follow this structure:

```markdown
# IT News Report — {topic}

> Generated on {date}

## Summary

A short paragraph summarizing the main trends found across all articles.

## Articles

### {Article Title}

- **Source**: {source name}
- **URL**: {url}
- **Published**: {date}

{2-3 sentence summary of the article content and its significance for IT professionals}

---

(repeat for each article)

## Key Trends

A bullet list of the main technology trends identified across all articles.
```

Save the final report to `/workspace/news-report-{YYYYMMDD-HHMMSS}.md` using the `write_file` tool, where `{YYYYMMDD-HHMMSS}` is the current date and time (e.g. `news-report-20260318-143012.md`).
To get the current timestamp, run:

```bash
date +"%Y%m%d-%H%M%S"
```

---
> Source: [aiden-liu/news_roundup_agent](https://github.com/aiden-liu/news_roundup_agent) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-19 -->
