---
name: integrate-rate-limit
description: Add rate limiting protection to endpoints. Use for throttling API requests. Use when this capability is needed.
metadata:
  author: oungseik
---

Integrate rate limiting into SvelteKit routes or ORPC handlers.

Update:
- Hook into `rate-limit.ts` instance
- Add rate limiter check before processing
- Throw appropriate errors for exceeded limits

Rate limiting conventions:
- Use 20 req/sec default based on rateLimiterMemory
- Differentiate by IP or session in production
- Handle rate-limit errors gracefully in UI
- Log or monitor excessive requests

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/oungseik) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
