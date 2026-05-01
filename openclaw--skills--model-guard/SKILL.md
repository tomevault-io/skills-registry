---
name: model-guard
description: Automatically monitors Anti-Gravity model quotas and switches the default model to the one with the highest remaining quota. If all Anti-Gravity models are below 20%, it falls back to the native `gemini-flash` model. Use when this capability is needed.
metadata:
  author: openclaw
---
# Model Guard

Automatically monitors Anti-Gravity model quotas and switches the default model to the one with the highest remaining quota. If all Anti-Gravity models are below 20%, it falls back to the native `gemini-flash` model.

## Usage

- **Manual trigger**: `model-guard`
- **Auto trigger**: Designed to be run via `cron` or `heartbeat`.

## Configuration

Edit `guard.js` to change the `THRESHOLD` (default 20%) or `FALLBACK_MODEL`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
