---
name: gemini-image-generator
description: Generate images using Google Gemini with customizable options Use when this capability is needed.
metadata:
  author: mkdev-me
---

# gemini-image-generator

## Instructions

Use this skill to generate images using Google Gemini's image generation model. The skill supports:
- Text-to-image generation from prompts
- Image-to-image generation with a reference image
- Multiple output sizes (1K, 2K, 4K)
- Custom output paths

The API key must be set via the `GEMINI_API_KEY` environment variable.

## Parameters

- `--prompt` (required): The text prompt describing the image to generate
- `--output` (required): Output file path for the generated image
- `--reference`: Optional reference image for style/content guidance
- `--size`: Image size - "1K", "2K", or "4K" (default: 4K)

## Examples

### Basic text-to-image generation
```bash
./scripts/generate.py --prompt "A serene mountain landscape at sunset" --output images/landscape.png
```

### With reference image for style guidance
```bash
./scripts/generate.py --prompt "Same character but wearing a party hat" --reference images/character.png --output images/party.png
```

### Different output size
```bash
./scripts/generate.py --prompt "Abstract art" --output art.png --size 2K
```

## Setup

Before first use, set up the virtual environment:
```bash
cd scripts && python3 -m venv venv && ./venv/bin/pip install -r requirements.txt
```

Set your API key:
```bash
export GEMINI_API_KEY="your-api-key-here"
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mkdev-me) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
