---
name: uiux-web
description: Web adapter for uiux-core. Adds web-specific overrides and checks while preserving deterministic UIUX Pack artifact names. Use when this capability is needed.
metadata:
  author: shunta-sato
---

## Purpose

Apply web-specific constraints/checks to an existing UIUX Pack.

## When to use

- `ui_contract.yaml` includes `web` in platforms.
- Browser UI flows/components/navigation are in scope.

## How to use

1) Open `references/uiux-web.md`.
2) Assume UIUX Pack already exists (created by `$uiux-core`).
3) Update only:
   - `ui_contract.yaml` under `platform_overrides.web`
   - `auto_review.json` by adding checks with `web.` prefix.
4) Do not add new artifact names.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/shunta-sato) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-12 -->
