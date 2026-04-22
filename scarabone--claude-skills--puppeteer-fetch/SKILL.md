---
name: puppeteer-fetch
description: Fallback web fetcher using headless browser with stealth mode. Use ONLY after web_fetch fails with 403, captcha, access denied, empty content, or "enable JavaScript" errors. Never use preemptively — always try web_fetch first. Use when this capability is needed.
metadata:
  author: scarabone
---

# Puppeteer Fetch

Fallback tool when `web_fetch` fails due to bot detection or JavaScript requirements.

## Decision Flow

```
1. User requests URL content
2. Try web_fetch first (ALWAYS)
3. If web_fetch succeeds → done
4. If web_fetch fails with blocking indicators → use puppeteer-fetch:fetch_page
```

## Trigger Conditions (ONLY after web_fetch failure)

Use puppeteer-fetch when web_fetch returns:
- 403 Forbidden
- Captcha or challenge page content
- "Please enable JavaScript" or similar
- Cloudflare/bot protection page
- Empty or truncated content from JS-heavy sites
- Access denied or blocked messages

## Never Use When

- web_fetch hasn't been tried yet
- web_fetch succeeded (even partially)
- User just wants a simple search (use web_search)
- Preemptively "just in case" — always try web_fetch first

## Tool Parameters

```
puppeteer-fetch:fetch_page
├── url (required): Full URL to fetch
├── timeout (optional): ms, default 30000
└── waitFor (optional): CSS selector to wait for before extracting
```

## Output

Returns markdown (not HTML).

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/scarabone) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
