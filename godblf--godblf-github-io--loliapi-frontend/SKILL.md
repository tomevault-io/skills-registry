---
name: loliapi-frontend
description: Integrate LoliAPI endpoints (https://www.loliapi.com) into frontend pages and components with framework-agnostic Fetch workflows. Use when requests involve random ACG images, avatars, or background images; selecting acg/pc/pe/pp/bg endpoints; handling id and type=url parameters; implementing loading/error states; adding timeout/retry/cache behavior; or troubleshooting redirect/CORS/image-loading issues. Use when this capability is needed.
metadata:
  author: godblf
---

# LoliAPI Frontend

## Overview

Implement reliable frontend integrations for LoliAPI image endpoints.
Default to framework-agnostic JavaScript + Fetch and adapt to React/Vue only when explicitly requested.

## Workflow Decision Tree

1. Determine output type.
- Need a visible image quickly: use endpoint URL directly as `<img src>`.
- Need the source URL string for composition/logging/storage: call endpoint with `type=url`.
2. Determine endpoint family.
- Adaptive image: `/acg/`
- Desktop image: `/acg/pc/`
- Mobile image: `/acg/pe/`
- Avatar image: `/acg/pp/`
- Random background image: `/bg/`
3. Determine randomness strategy.
- Random image: omit `id`.
- Stable image: pass `id=<integer>`.
4. Apply resilience defaults.
- Validate inputs before request.
- Apply timeout via `AbortController`.
- Retry once for transient network failures.
- Provide loading, success, and error UI states.
5. Escalate to diagnostics.
- Read `references/troubleshooting.md` when response shape, redirect behavior, or rendering is inconsistent.

## Required Output Contract

When generating integration code, include all items below unless the user explicitly opts out.

1. Expose one URL builder function that accepts `endpoint`, optional `id`, and optional `type`.
2. Use `URL` and `URLSearchParams` instead of string concatenation.
3. Include timeout handling and user-facing error fallback.
4. Document whether code relies on redirect behavior or `type=url`.
5. Keep code framework-agnostic by default; adapt only when requested.

## Reference Loading Rules

1. Read `references/endpoints.md` first for endpoint capabilities and parameter behavior.
2. Read `references/frontend-patterns.md` when generating concrete code.
3. Read `references/troubleshooting.md` only when debugging or hardening behavior.
4. Treat `/getip/` as low-trust documentation unless independently verified.

## Guardrails

1. Never assume `/getip/` examples are correct; the source document section is inconsistent.
2. Avoid assumptions beyond documented endpoint behavior.
3. Prefer deterministic fallback behavior:
- If `id` is invalid, drop `id` and fetch random.
- If `type` is invalid, omit `type` and accept default redirect.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/godblf) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
