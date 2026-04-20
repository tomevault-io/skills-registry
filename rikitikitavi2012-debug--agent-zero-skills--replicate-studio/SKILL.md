---
name: replicate-studio
description: AI generation with Replicate API. Create images, music, upscale photos, transcribe audio using models like Flux, SDXL, MusicGen. Use when user needs AI content generation, image creation, audio processing, or model inference. Use when this capability is needed.
metadata:
  author: rikitikitavi2012-debug
---

# Replicate Studio — AI Generation

Generate AI content using Replicate API. Supports image generation, audio processing, upscaling, and transcription.

## Installation

```bash
# Install dependencies
pip install -r requirements.txt

# Or use setup script
bash /a0/usr/skills/setup.sh

# Set API token (get at https://replicate.com)
export REPLICATE_API_TOKEN="your_token_here"
# Or add to /a0/.env: REPLICATE_API_TOKEN=your_token_here
```

## When to Use

Use this skill when you need to:
- Generate images from text prompts
- Upscale low-resolution images
- Create music or audio
- Transcribe speech to text
- Run AI model inference

## Supported Models

| Model | Type | Best For |
|-------|------|----------|
| **flux-pro** | Image | High-quality images |
| **flux-schnell** | Image | Fast image generation |
| **sdxl** | Image | Detailed images |
| **musicgen** | Audio | Music generation |
| **esrgan** | Image | 4x image upscaling |
| **whisper** | Audio | Speech transcription |

## Usage

### Via Python Script
```bash
python /a0/usr/skills/replicate-studio/scripts/replicate_studio.py --model flux-pro --prompt "a beautiful sunset" --output /a0/tmp/sunset.png
```

### Parameters

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `--model` | str | required | Model to use (flux-pro/flux-schnell/sdxl/musicgen/esrgan/whisper) |
| `--prompt` | str | required | Input prompt or description |
| `--output` | str | required | Output file path |
| `--input_image` | str | optional | Input image for upscaling |
| `--input_audio` | str | optional | Input audio for transcription |

### Examples

1. **Generate image:**
   ```bash
   python /a0/usr/skills/replicate-studio/scripts/replicate_studio.py --model flux-pro --prompt "futuristic city at night, neon lights, cyberpunk style" --output /a0/tmp/city.png
   ```

2. **Upscale image:**
   ```bash
   python /a0/usr/skills/replicate-studio/scripts/replicate_studio.py --model esrgan --input_image /a0/tmp/photo.png --output /a0/tmp/photo_4x.png
   ```

3. **Transcribe audio:**
   ```bash
   python /a0/usr/skills/replicate-studio/scripts/replicate_studio.py --model whisper --input_audio /a0/tmp/recording.mp3 --output /a0/tmp/transcription.txt
   ```

## Requirements

- `REPLICATE_API_TOKEN` must be set in environment or `/a0/.env` file
- `replicate>=0.22.0`
- `requests>=2.31.0`

## Files

```
/a0/usr/skills/replicate-studio/
├── scripts/
│   └── replicate_studio.py
├── requirements.txt
└── SKILL.md
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rikitikitavi2012-debug) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
