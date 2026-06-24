---
name: drupal-best-practices
description: Extend node-oriented conventions to media, paragraphs, comments, and related entities while accounting for translation and display nuances. Use when this capability is needed.
metadata:
  author: theodorosploumis
---

## Description
Extend node-oriented conventions to media, paragraphs, comments, and related entities while accounting for translation and display nuances.

## Usage
- Ask Claude to mirror node naming, documentation, and view mode reuse when modeling non-node entities.
- Request guidance on translation handling, especially for paragraphs.
- Have Claude suggest image style names that describe intent instead of dimensions.

## Guardrails
- Keep machine names concise and reusable across bundles.
- Ensure translation workflows are explicit for nested entities like paragraphs.
- Prefer descriptive image style names (goal-based, not pixel-based) and avoid GIF unless required.

## Validation
Refer to scripts aligned with the specific entity (e.g., fields, views, blocks) after applying changes.

## References
- Drupal Best Practices README.
- Apply node naming, descriptions, and view mode reuse patterns consistently.

---
> Source: [theodorosploumis/drupal-best-practices](https://github.com/theodorosploumis/drupal-best-practices) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-21 -->
