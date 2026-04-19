---
name: history
description: Show recent git activity in readable format. Part of chief-of-staff system. Use when this capability is needed.
metadata:
  author: bradautomates
---

# /history - Recent Activity

Show chief-of-staff activity from git log.

## Run

```bash
git log --since="7 days ago" --grep="cos:" --format="%ad %s" --date=short
```

Group output by day and summarize actions.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/bradautomates) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
