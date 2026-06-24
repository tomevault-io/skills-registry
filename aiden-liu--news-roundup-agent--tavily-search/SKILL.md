---
name: tavily-search
description: search the web using Tavily API with a query as argument. Use this skill when the user asks to search for information on the web. Use when this capability is needed.
metadata:
  author: aiden-liu
---
# Tavily Search

## Command to execute

```bash
curl -s -X POST https://api.tavily.com/search \
  -H 'Content-Type: application/json' \
  -H "Authorization: Bearer ${TAVILY}" \
  -d "{\"query\": \"$ARGUMENTS_REST\", \"search_depth\": \"advanced\", \"max_results\": 6, \"time_range\": \"week\"}"
```

---
> Source: [aiden-liu/news_roundup_agent](https://github.com/aiden-liu/news_roundup_agent) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-19 -->
