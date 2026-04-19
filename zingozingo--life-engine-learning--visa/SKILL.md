---
name: visa
description: Visa requirements and travel document information Use when this capability is needed.
metadata:
  author: zingozingo
---

# Visa Skill

## When to Use

Use this skill for questions about visa requirements, entry requirements, travel documents, or whether a passport holder needs a visa for a specific destination.

## Tool Usage

```
mock_api_fetch("visa", {
    "passport": "US",
    "destination": "Japan"
})
```

Use 2-letter country codes for passports (US, UK, CA, AU, DE, FR, etc.) and full country names for destinations.

For detailed response handling, formatting examples, and edge cases, see `references/response_guide.md`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/zingozingo) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
