---
name: chatgpt2api-search
description: Use when current web search is needed through this chatgpt2api server. Call the configured HTTP search endpoint with a prompt and return the answer with source URLs.
metadata:
  author: basketikun
---

# ChatGPT2API Search

Use this skill when the user asks for current web search, online lookup, recent information, or source-backed answers.

## Endpoint

POST http://127.0.0.1:8000/v1/search

Headers:

Authorization: Bearer chatgpt2api
Content-Type: application/json

Body:

{
  "prompt": "<search question>"
}

## Response

Use `answer` as the main response. Include URLs from `sources` when available.

---
> Source: [basketikun/chatgpt2api](https://github.com/basketikun/chatgpt2api) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-27 -->
