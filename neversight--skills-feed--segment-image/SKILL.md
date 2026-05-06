---
name: segment-image
description: This skill should be used when the user asks to "segment an image", "identify objects", "extract objects", "generate masks", "find objects in image", or needs AI-powered image segmentation. Use when this capability is needed.
metadata:
  author: neversight
---

# Segment Image

Segment and identify objects in images using Gemini's vision capabilities.

## When to Use

Use this skill when the user asks to:
- Identify objects in an image
- Generate masks for specific objects
- Segment an image into regions
- Extract objects from an image

## Usage

```bash
cd ${CLAUDE_PLUGIN_ROOT}/skills/segment-image && bun run scripts/segment.ts <input-image> [options]
```

### Options

- `--prompt <text>` - Custom segmentation prompt
- `--output <dir>` - Output directory for mask files

### Examples

```bash
cd ${CLAUDE_PLUGIN_ROOT}/skills/segment-image

# Segment all objects
bun run scripts/segment.ts photo.jpg

# Segment with custom prompt
bun run scripts/segment.ts photo.jpg --prompt "identify all people and vehicles"

# Save masks to directory
bun run scripts/segment.ts photo.jpg --output ./masks
```

## Model

Uses `gemini-3-pro-preview` for image segmentation.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neversight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
