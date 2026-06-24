---
name: conversations-by-date
description: Browse conversations by calendar date. Use when user asks "what was I working on last Thursday" or specifies a date. Use when this capability is needed.
metadata:
  author: mordechaipotash
---

# conversations-by-date — L1 temporal browse

## Framework context

L1 retrieval scoped to a single date. Returns distinct conversation_ids with titles and sources. Pure function of (date, corpus).

## When to invoke

- User cites a specific date: "last Tuesday", "yesterday", "on 2026-04-01"
- Pair with `daily-memory-file` for narrative reconstruction
- Pre-step before tunnel-state when the domain is unknown but the date is known

## Input

```
date: str        # YYYY-MM-DD
limit: int = 30
```

## Output contract

Markdown list of distinct conversations from that date. Each row: title, source, model, created timestamp, conv_id citation.

## Does NOT do

- Handle relative date strings ("yesterday") — caller must resolve to YYYY-MM-DD
- Return messages — only conversation summaries
- Fabricate empty-day output — explicit "No conversations found on {date}"

## Verification checklist

- [ ] DATE cast on both sides of comparison (timezone-safe)
- [ ] DISTINCT on conversation_id
- [ ] ORDER BY created DESC

---
> Source: [mordechaipotash/brain-mcp](https://github.com/mordechaipotash/brain-mcp) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-21 -->
