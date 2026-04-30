---
name: grok-image
description: Generate images using Grok (xAI) image generation API. Use when you need to create images from text prompts. Use when this capability is needed.
metadata:
  author: duclm1x1
---

# Grok Image Generation

## Setup

Add your API key to `~/.clawdbot/.env`:
```
GROK_API_KEY=xai-your-key-here
```

## Usage

Generate images with a simple prompt:

```
Generate a cute raccoon character with friendly smile
```

The skill will:
1. Read GROK_API_KEY from ~/.clawdbot/.env
2. Call Grok's image generation API
3. Save the image and return the path

## Options

- **Custom output path**: Add `--output /path/to/image.png`
- **Size**: `--size 512x512` (default: 1024x1024)

## Notes

- Uses grok-imagine-image model
- Images are saved to the workspace or specified path

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/duclm1x1) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
