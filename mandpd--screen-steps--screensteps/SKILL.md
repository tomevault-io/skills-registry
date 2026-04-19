---
name: screensteps
description: Retrieve and analyze Screen Steps articles via the Screen Steps API. Use when a user asks to list spaces, list or search articles, fetch an article by ID/title, or assess whether a specific article answers a question. Requires a user-supplied Screen Steps base URL and username/password for Basic Auth. Use when this capability is needed.
metadata:
  author: mandpd
---

# Screensteps

## Overview

Retrieve articles from a Screen Steps instance using its API, then summarize or answer questions based on the retrieved content.

## Required Inputs

- Prompt for the Screen Steps base URL (e.g., `https://<org>.screenstepslive.com`).
- Prompt for Basic Auth credentials (username and password).
- If needed, ask for a site name or ID to scope results.

## Core Capabilities

1. List sites.
2. List articles (optionally scoped to a site).
3. Search articles by title or query.
4. Fetch a specific article by ID or an exact title match.
5. Evaluate whether an article answers a user’s question.

## Workflow

1. Collect base URL and Basic Auth credentials.
2. Confirm or infer the API root for the user’s Screen Steps instance.
3. Load `references/screensteps-api.md` for endpoint patterns and parameters.
3. Execute the API calls needed for the task:
   - List sites when the user asks about available sites or wants to scope by site.
   - List or search articles when the user asks for “what articles are available” or requests topic discovery.
   - Fetch a specific article when the user references an article by name or ID.
4. Normalize results into a concise list:
   - Title
   - URL
   - Space (if available)
   - Short summary or excerpt (if needed)
5. Answer the user’s question using the retrieved content.

If you cannot find the correct API root or endpoint, ask the user for their Screen Steps API documentation or a working example curl request, then proceed.

## Task Guidance

- “What articles are available that could help with X?”
  - Search articles by title/query, then return the most relevant items and ask if the user wants deeper summaries.
- “Does article X help with Y?”
  - Fetch article X, summarize key sections, then answer explicitly whether it covers Y and what gaps remain.
- “What articles are available in site X?”
  - Resolve site X (by name or ID), then list its articles (optionally paginated).

## Output Expectations

- Provide a short, scannable list of matching articles with titles and URLs.
- Offer to refine results (by space, topic, or keywords) when the list is long.
- If credentials fail, report the authentication error and ask for corrected credentials.

## Example Triggers

- “What articles are available that could help with X?”
- “Does article X help with Y?”
- “What articles are available in space X?”

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mandpd) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
