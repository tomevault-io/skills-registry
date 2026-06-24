---
name: brave-web-search
description: search the web using Brave Search API with a query as argument. Use this skill when the user asks to search for information on the web. Use when this capability is needed.
metadata:
  author: aiden-liu
---
# Brave Web Search

## Command to execute

```bash
curl -s "https://api.search.brave.com/res/v1/web/search?q=$(echo "$ARGUMENTS_REST" | sed 's/ /+/g')&count=10" \
  -H "X-Subscription-Token: ${BRAVE}" \
  -H "Accept: application/json"
```

---
> Source: [aiden-liu/news_roundup_agent](https://github.com/aiden-liu/news_roundup_agent) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-19 -->
