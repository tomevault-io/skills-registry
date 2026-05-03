---
name: xai-imagine
description: Generate high-quality images using xAI's Grok Imagine API (grok-imagine-image). Supports custom prompts, multiple aspect ratios (1:1, 16:9, 9:16, etc.), and 1k resolution. Use when the user wants to create images or visual content via xAI. Use when this capability is needed.
metadata:
  author: nitekat1124
---

# xAI Imagine

Use xAI's Grok Imagine API to generate images based on text prompts.

## Quick Start

To generate an image, use the `scripts/generate.js` script. It handles the API call and returns the image URL.

### Triggers
- "xai 畫..."
- "imagine [prompt]"
- "用 grok 畫一個..."

## Aspect Ratios
Supported ratios: "1:1", "3:4", "4:3", "9:16", "16:9", "2:3", "3:2", "9:19.5", "19.5:9", "9:20", "20:9", "1:2", "2:1", "auto".

## Usage Example

> 需先設定 `XAI_API_KEY`：
> - 建議在環境變數中設 `XAI_API_KEY`
> - 或放在 `~/.openclaw/.env`（格式：`XAI_API_KEY=...`）


```bash
# Generate a 16:9 image
node scripts/generate.js --prompt "A futuristic neon city with a black cat" --ratio "16:9"

# Equivalent (equals style)
node scripts/generate.js --prompt="A futuristic neon city with a black cat" --ratio="16:9"
```

## Workflow
1. Identify the prompt and desired aspect ratio (default is 1:1).
2. Execute `scripts/generate.js` with the `--prompt` and optional `--ratio`.
3. The script will output a JSON with the image URL.
4. Use the `message` tool to deliver the image to the user.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/nitekat1124) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
