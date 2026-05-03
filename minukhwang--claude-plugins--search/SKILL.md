---
name: search
description: | Use when this capability is needed.
metadata:
  author: minukhwang
---

# Current Date Awareness for Web Search

## MANDATORY: Check Current Date Before Search

Before using `WebSearch` or `WebFetch`, ALWAYS run:

```bash
date +%Y-%m-%d
```

Use the returned year (e.g., 2025) in your search queries.

## Include Current Year in Queries

For time-sensitive topics (tech docs, versions, news, trends), append the current year:

| Bad Query | Good Query |
|-----------|------------|
| "React 19 features" | "React 19 features 2025" |
| "Next.js latest version" | "Next.js latest version 2025" |
| "AI trends" | "AI trends 2025" |

**NEVER assume or hardcode the year. ALWAYS check first.**

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/minukhwang) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
