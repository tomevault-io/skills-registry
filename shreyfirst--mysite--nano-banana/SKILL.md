---
name: nano-banana
description: Generate and edit images using Nano Banana via OpenRouter API. Use when the user asks to create images, generate assets, edit visuals, make icons, illustrations, or any image generation task for this project. Use when this capability is needed.
metadata:
  author: shreyfirst
---

# Nano Banana Image Generation

Generate high-quality images using Google's Gemini 3 Pro Image Preview model via OpenRouter.

## Setup

The `.env` file is already configured with the API key. Scripts are located at:
- Bash: `.cursor/skills/nano-banana/scripts/nano-banana.sh`
- Node.js: `.cursor/skills/nano-banana/scripts/nano-banana.js`

## Quick Start

Run from the project root directory:

### Generate a new image

```bash
.cursor/skills/nano-banana/scripts/nano-banana.sh "A modern minimalist logo, blue gradient" -o logo.png
```

### Edit an existing image

```bash
.cursor/skills/nano-banana/scripts/nano-banana.sh "Make the background darker" -i image.png -o edited.png
```

### Use multiple reference images

```bash
.cursor/skills/nano-banana/scripts/nano-banana.sh "Combine styles" -i ref1.png -i ref2.png -o combined.png
```

## CLI Options

| Option | Description |
|--------|-------------|
| `-o`, `--output` | Output path for generated image |
| `-i`, `--input` | Input image(s) for editing (can specify multiple) |
| `-m`, `--model` | Model ID (default: google/gemini-3-pro-image-preview) |

## Common Use Cases

### Website Assets

```bash
# Favicon
.cursor/skills/nano-banana/scripts/nano-banana.sh "Minimalist favicon, letter S, modern tech style" -o public/favicon.png

# Hero image
.cursor/skills/nano-banana/scripts/nano-banana.sh "Abstract gradient hero, purple to blue" -o public/hero.png

# Social preview
.cursor/skills/nano-banana/scripts/nano-banana.sh "Social media preview card, 1200x630" -o public/og-image.png
```

### Icons

```bash
.cursor/skills/nano-banana/scripts/nano-banana.sh "Minimalist line icon for home" -o assets/icons/home.png
.cursor/skills/nano-banana/scripts/nano-banana.sh "Minimalist line icon for mail" -o assets/icons/mail.png
```

### Image Editing

```bash
# Remove background
.cursor/skills/nano-banana/scripts/nano-banana.sh "Remove background, make transparent" -i photo.jpg -o photo-nobg.png

# Style transfer
.cursor/skills/nano-banana/scripts/nano-banana.sh "Apply cyberpunk neon aesthetic" -i original.png -o styled.png
```

## Node.js Programmatic Use

```javascript
const { generateImage } = require('./.cursor/skills/nano-banana/scripts/nano-banana.js');

await generateImage({
  prompt: "A beautiful sunset over mountains",
  outputPath: "assets/sunset.png",
  inputPaths: [] // optional reference images
});
```

## Troubleshooting

**OPENROUTER_API_KEY not set**
- The `.env` file should exist in project root with the key

**No image in response**
- Rephrase prompt to explicitly request image generation
- Prefix with "Generate an image of..."

**Model not available**
- Try fallback: `-m google/gemini-2.0-flash-exp:free`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/shreyfirst) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
