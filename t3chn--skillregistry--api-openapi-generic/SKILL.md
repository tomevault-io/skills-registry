---
name: api-openapi-generic
description: How to integrate external APIs safely; how to store and query OpenAPI without polluting context. Use when this capability is needed.
metadata:
  author: t3chn
---

# API Integration (Generic)

- Prefer finding OpenAPI/Swagger spec; store it in `references/` (not pasted into chat).
- Implement:
  - timeouts
  - retries with backoff
  - idempotency where required
  - clear error classification (retryable vs non-retryable)
- Keep a project overlay skill `api-<vendor>` with endpoints used by the project.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/t3chn) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
