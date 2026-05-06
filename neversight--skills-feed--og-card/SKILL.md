---
name: og-card
description: | Use when this capability is needed.
metadata:
  author: neversight
---

# /og-card

## vs /og-hero-image
- /og-hero-image: AI creative via Gemini. Use for one-off hero art.
- /og-card: consistent templates via Satori. Use for branded systems.

## Templates
- blog: title + author + date + brand colors
- product: logo + tagline + screenshot
- changelog: version + highlights
- comparison: product vs competitor

## Process
1. Read `brand-profile.yaml` for colors/fonts when present.
2. Select a template and pass required fields.
3. Render via `skills/og-card/scripts/generate-card.ts`.
4. Emit a 1200x630 PNG.

## Prerequisites
`pnpm add @vercel/og satori sharp`

## Usage
`/og-card blog "Title" by Author`
`/og-card product for heartbeat`

## Output
`og-[template]-[slug].png` at 1200x630

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neversight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
