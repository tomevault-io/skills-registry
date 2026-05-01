---
name: broken-link-checker
description: verify external URLs (http/https) for availability (200-399 status code). Use when this capability is needed.
metadata:
  author: openclaw
---

# Broken Link Checker

Verify external URLs for availability. Useful for checking documentation links or external references.

## Usage

```bash
node skills/broken-link-checker/index.js <url1> [url2...]
```

## Output

JSON array of results:
```json
[
  {
    "url": "https://example.com",
    "valid": true,
    "status": 200
  },
  {
    "url": "https://example.com/broken",
    "valid": false,
    "status": 404
  }
]
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
