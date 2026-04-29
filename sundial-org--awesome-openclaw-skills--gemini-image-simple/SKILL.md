---
name: gemini-image-simple
description: Generate and edit images with Gemini API using pure Python stdlib. Zero dependencies - works on locked-down environments where pip/uv aren't available. Use when this capability is needed.
metadata:
  author: sundial-org
---

# Gemini Image Simple

Generate and edit images using Google's Gemini 2.0 Flash image generation API.

## Why This Skill

| Feature | This Skill | Others (nano-banana-pro, etc.) |
|---------|------------|-------------------------------|
| **Dependencies** | None (stdlib only) | google-genai, pillow, etc. |
| **Requires pip/uv** | ❌ No | ✅ Yes |
| **Works on Fly.io free** | ✅ Yes | ❌ Fails |
| **Works in containers** | ✅ Yes | ❌ Often fails |
| **Image generation** | ✅ Full | ✅ Full |
| **Image editing** | ✅ Yes | ✅ Yes |
| **Setup complexity** | Just set API key | Install packages first |

**Bottom line:** This skill works anywhere Python 3 exists. No package managers, no virtual environments, no permission issues.

## Quick Start

```bash
# Generate
python3 /data/clawd/skills/gemini-image-simple/scripts/generate.py "A cat wearing a tiny hat" cat.png

# Edit existing image  
python3 /data/clawd/skills/gemini-image-simple/scripts/generate.py "Make it sunset lighting" edited.png --input original.png
```

## Usage

### Generate new image

```bash
python3 {baseDir}/scripts/generate.py "your prompt" output.png
```

### Edit existing image

```bash
python3 {baseDir}/scripts/generate.py "edit instructions" output.png --input source.png
```

Supported input formats: PNG, JPG, JPEG, GIF, WEBP

## Environment

Set `GEMINI_API_KEY` environment variable. Get one at https://aistudio.google.com/apikey

## How It Works

Uses Gemini 2.0 Flash experimental image generation:
- Pure `urllib.request` for HTTP (no requests library)
- Pure `json` for parsing (stdlib)
- Pure `base64` for encoding (stdlib)

That's it. No external packages. Works on any Python 3.10+ installation.

## Examples

```bash
# Landscape
python3 {baseDir}/scripts/generate.py "Misty mountains at sunrise, photorealistic" mountains.png

# Product shot
python3 {baseDir}/scripts/generate.py "Minimalist product photo of a coffee cup, white background" coffee.png

# Edit: change style
python3 {baseDir}/scripts/generate.py "Convert to watercolor painting style" watercolor.png --input photo.jpg

# Edit: add element
python3 {baseDir}/scripts/generate.py "Add a rainbow in the sky" rainbow.png --input landscape.png
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sundial-org) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
