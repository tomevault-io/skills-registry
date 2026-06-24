---
name: news-roundup
description: search the news using Tavily News Search API with a query as argument. Use this skill when the user asks to search for recent news or current events. Use when this capability is needed.
metadata:
  author: aiden-liu
---
# News Roundup

## Purpose

Generate a comprehensive Markdown news report on a given topic (default: "artificial intelligence, the open-source ecosystem, data engineering/modelling, software engineering, cloud infrastructure, cybersecurity").

## Steps to follow

### Step 1 — Search for recent news

#### Command to execute

```bash
curl -s -X POST https://api.tavily.com/search \
  -H 'Content-Type: application/json' \
  -H "Authorization: Bearer ${TAVILY}" \
  -d '{"query": "$ARGUMENTS_REST", "search_depth": "advanced", "max_results": 3, "time_range": "week"}'
```

### Step 2 — Enrich each article

For each article returned in Step 1, use the below command with the article URL to retrieve additional context and details.

#### Command to execute

```bash
curl -s -X POST https://api.tavily.com/search \
  -H 'Content-Type: application/json' \
  -H "Authorization: Bearer ${TAVILY}" \
  -d '{"query": "$ARTICLE_URL", "max_results": 4}'
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

Save the final report to `/workspace/data/news-report-{YYYYMMDD-HHMMSS}.md` using tool `write_file`, where `{YYYYMMDD-HHMMSS}` is the current date and time (e.g. `news-report-20260318-143012.md`).
To get the current timestamp, run:

```bash
date +"%Y%m%d-%H%M%S"
```

---
> Source: [aiden-liu/news_roundup_agent](https://github.com/aiden-liu/news_roundup_agent) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-19 -->
