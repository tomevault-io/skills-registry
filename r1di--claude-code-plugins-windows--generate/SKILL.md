---
name: generate
description: Nano Banana Pro (nano-banana-pro) image generation skill. Use this skill when the user asks to "generate an image", "generate images", "create an image", "make an image", uses "nano banana", or requests multiple images like "generate 5 images". Generates images using Google's Gemini 2.5 Flash for any purpose - frontend designs, web projects, illustrations, graphics, hero images, icons, backgrounds, or standalone artwork. Invoke this skill for ANY image generation request. Use when this capability is needed.
metadata:
  author: r1di
---

# Nano Banana Pro - Gemini Image Generation (Windows Compatible)

Generate custom images using Google's Gemini models for integration into frontend designs.

## Prerequisites

### Windows Setup

1. Install [uv](https://docs.astral.sh/uv/) - Python package manager:
   ```powershell
   # PowerShell (as Admin)
   irm https://astral.sh/uv/install.ps1 | iex
   ```

2. Set the `GEMINI_API_KEY` environment variable:
   ```powershell
   # Temporary (current session only)
   $env:GEMINI_API_KEY = "your-api-key-here"

   # Permanent (User level)
   [Environment]::SetEnvironmentVariable("GEMINI_API_KEY", "your-api-key-here", "User")
   ```

   Or via Windows Settings:
   - Search "Environment Variables" in Start Menu
   - Click "Edit environment variables for your account"
   - Add new variable: `GEMINI_API_KEY` with your API key

## Available Models

| Model | ID | Best For | Max Resolution |
|-------|-----|----------|----------------|
| **Flash** | `gemini-2.5-flash-image` | Speed, high-volume tasks | 1024px |
| **Pro** | `gemini-3-pro-image-preview` | Professional quality, complex scenes | Up to 4K |

## Image Generation Workflow

### Step 1: Generate the Image

Use `scripts/image.py` with uv:

```bash
uv run "${SKILL_DIR}/scripts/image.py" \
  --prompt "Your image description" \
  --output "/path/to/output.png"
```

**Windows PowerShell:**
```powershell
uv run "$env:SKILL_DIR\scripts\image.py" `
  --prompt "Your image description" `
  --output "C:\path\to\output.png"
```

Options:
- `--prompt` (required): Detailed description of the image to generate
- `--output` (required): Output file path (PNG format)
- `--aspect` (optional): Aspect ratio - "square", "landscape", "portrait" (default: square)
- `--reference` (optional): Path to a reference image for style guidance
- `--model` (optional): Model to use - "flash" (fast) or "pro" (high-quality) (default: flash)
- `--size` (optional): Image resolution for pro model - "1K", "2K", "4K" (default: 1K)

### Using Different Models

**Flash model (default)** - Fast generation:
```bash
uv run "${SKILL_DIR}/scripts/image.py" \
  --prompt "A minimalist logo design" \
  --output "/path/to/logo.png" \
  --model flash
```

**Pro model** - Higher quality:
```bash
uv run "${SKILL_DIR}/scripts/image.py" \
  --prompt "A detailed hero illustration" \
  --output "/path/to/hero.png" \
  --model pro \
  --size 2K
```

## Crafting Effective Prompts

Write detailed, specific prompts for best results:

**Good prompt:**
> A minimalist geometric pattern with overlapping translucent circles in coral, teal, and gold on a deep navy background, suitable for a modern fintech landing page hero section

**Avoid vague prompts:**
> A nice background image

### Prompt Elements to Include

1. **Subject**: What the image depicts
2. **Style**: Artistic style (minimalist, abstract, photorealistic, illustrated)
3. **Colors**: Specific color palette
4. **Mood**: Atmosphere (professional, playful, elegant, bold)
5. **Context**: How it will be used (hero image, icon, texture)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/r1di) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
