---
name: nano-banana-pro
description: AI image generation using Google Gemini's gemini-3-pro-image-preview model. Simple script to generate images from text prompts. Use when user asks to generate, create, or draw images with Gemini. Use when this capability is needed.
metadata:
  author: sanyuan0704
---

# Image Generation with Gemini

Generate images using Google's Gemini model (`gemini-3-pro-image-preview`).

## Script Directory

**Agent Execution**:
1. `SKILL_DIR` = this SKILL.md file's directory
2. Script path = `${SKILL_DIR}/scripts/generate_image.py`

## Prerequisites

- Python 3.8+
- Google API key with Gemini access
- `google-genai` package installed

## Installation

```bash
pip install google-genai
```

## Environment Variables

| Variable | Description |
|----------|-------------|
| `GOOGLE_API_KEY` | Google API key (required) |

## Usage

```bash
# Basic usage
python ${SKILL_DIR}/scripts/generate_image.py --prompt "A serene mountain landscape" --output "output.png"

# With custom model (default: gemini-3-pro-image-preview)
python ${SKILL_DIR}/scripts/generate_image.py --prompt "A cute cat" --output "cat.png" --model "gemini-3-pro-image-preview"

# With temperature control
python ${SKILL_DIR}/scripts/generate_image.py --prompt "A futuristic city" --output "city.png" --temperature 0.8
```

## Options

| Option | Description |
|--------|-------------|
| `--prompt <text>` | Prompt text for image generation (required) |
| `--output <path>` | Output image filename (default: output.png) |
| `--model <id>` | Gemini image model (default: gemini-3-pro-image-preview) |
| `--temperature <float>` | Sampling temperature (default: 1.0) |

## How It Works

1. Takes a text prompt as input
2. Sends the prompt to Gemini's image generation API
3. Streams the response and extracts inline image data
4. Saves the generated image to the specified output file

## Example Prompts

- "A serene mountain landscape at sunset"
- "A cute cartoon cat playing with yarn"
- "A futuristic cityscape with flying cars"
- "A watercolor painting of a flower garden"
- "A minimalist logo design for a coffee shop"

## Error Handling

- If no API key is set, the script will fail with an authentication error
- If image generation fails, the script outputs "生成图片失败"
- On success, outputs "图片已保存到 {output_path}"

## Notes

- Uses streaming API for efficient response handling
- Supports PNG output format
- Model must support IMAGE response modality

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sanyuan0704) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
