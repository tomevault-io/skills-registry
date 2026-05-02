---
name: image-gen-road2all
description: Generate images via Road2all (OpenAI-compatible) Images API and save to workspace downloads by date. Use when this capability is needed.
metadata:
  author: magicwang1111
---

# image-gen-road2all

Generate images using the Road2all Images API (OpenAI-compatible), save them under:

- `{workspace}/downloads/YYYY-MM-DD/`

## Inputs

- `prompt` (required)
- `size` (optional, default: `1024x1024`) — e.g. `1024x1536`, `1536x1024`
- `n` (optional, default: `1`)
- `model` (optional, default: `gpt-image-1.5`)

## Auth

Reads Road2all config from:

- `~/.clawdbot/clawdbot.json` → `models.providers.road2all.baseUrl`
- `~/.clawdbot/clawdbot.json` → `models.providers.road2all.apiKey`

Optionally can be overridden by env:

- `ROAD2ALL_BASE_URL`
- `ROAD2ALL_API_KEY`

## Output

Returns a JSON payload with generated file paths.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/magicwang1111) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
