---
name: kusa
description: Generate images using the Kusa.pics API. Use when this capability is needed.
metadata:
  author: duclm1x1
---
# Kusa.pics Image Generator

Generate images using the Kusa.pics API.

## Configuration
- API Key: Set `KUSA_API_KEY` environment variable.

## Usage
```bash
export KUSA_API_KEY="your_api_key_here"
node skills/kusa-image/index.js "Your prompt here" [--style <id>] [--width <w>] [--height <h>]
```

## Options
- `--style`: Style ID (Default: 6)
- `--width`: Width (Default: 960)
- `--height`: Height (Default: 1680)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/duclm1x1) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
