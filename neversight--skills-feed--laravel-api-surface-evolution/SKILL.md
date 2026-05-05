---
name: laravelapi-surface-evolution
description: Evolve APIs safely using versioned DTOs/transformers, deprecations, and compatibility tests Use when this capability is needed.
metadata:
  author: neversight
---

# API Surface Evolution

Design for change without breaking clients.

## Versioning Strategy

- Choose explicit versioning (URI `/v1/...` or header negotiation)
- Default to additive changes; never break a released contract

## DTOs & Transformers

- Define versioned DTOs; map from models/services via transformers
- Keep controller thin—validate → transform → respond

## Deprecations

- Mark fields as deprecated in docs and responses (e.g., headers)
- Provide sunset timelines; add metrics to see remaining usage

## Testing

- Contract tests per version (request/response shapes)
- Backward compatibility tests for commonly used flows

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neversight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
