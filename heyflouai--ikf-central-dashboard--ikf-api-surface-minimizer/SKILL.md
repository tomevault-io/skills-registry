---
name: ikf-api-surface-minimizer
description: Designs minimal, secure API endpoints for heatmaps/analytics so the client never over-fetches or exposes sensitive forecast data. Use when this capability is needed.
metadata:
  author: heyflouai
---

# IKF API Surface Minimizer

## Objective
Define endpoints that return ONLY what UI needs, with auth checks and least-privilege access.

## Requirements
- No service-role keys in the client.
- Avoid SELECT * and broad reads.
- Prefer aggregated endpoints for analytics.
- Ensure endpoints are scoped by org_id and authenticated user.
- Return minimal fields: ticker, horizon, signal, pred (score optional internal).

## Output
- Endpoint list for Phase 1 + Phase 2 (today heatmap, compare drawer, wishlist rows, dashboard top/bottom, analytics summaries).
- Response schemas.
- Security checks per endpoint.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/heyflouai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
