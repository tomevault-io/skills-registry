---
name: lcp-mintlify-docs
description: Edit the Mintlify docs site under docs/. Keep docs.json consistent and run a11y/broken-links checks. Use when this capability is needed.
metadata:
  author: yusukeshimizu
---

You are editing the documentation site under `docs/` (Mintlify).

## Scope

- Site config: `docs/docs.json`
- Pages and assets: `docs/**`
- Local validation scripts: `docs/scripts/check-docs-json.mjs`

## Workflow

1. Prefer repo-root-relative links so the link checker resolves paths consistently.
2. Japanese pages mirror English pages using a `-ja` suffix and live alongside them.
3. Keep navigation (`docs/docs.json`) and page frontmatter consistent.

## Validation (run from `docs/`)

- `node scripts/check-docs-json.mjs`
- `npx --yes mintlify@4.2.255 a11y`
- `npx --yes mintlify@4.2.255 broken-links`

Optional local preview:

- `npx --yes mintlify@4.2.255 dev --no-open`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/yusukeshimizu) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
