---
name: googlesearch
description: Gemini Grounding with Google Search - Real-time web data Use when this capability is needed.
metadata:
  author: who-visions
---

# Google Search Skill

Ground Gemini responses with real-time web search.

## Features

- **Reduce Hallucinations**: Ground answers in real data
- **Real-Time Info**: Access current events and news
- **Citations**: Verifiable sources for claims

## Usage

```python
from rhea_noir.skills.googlesearch.actions import skill as gs

result = gs.search(
    query="Who won the 2024 Super Bowl?",
    model="gemini-3-flash-preview"
)
print(result["answer"])
print(result["sources"])
```

## With URL Context

```python
result = gs.search_with_urls(
    query="Summarize this article and add recent updates",
    urls=["https://example.com/article"],
    model="gemini-3-flash-preview"
)
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/who-visions) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
