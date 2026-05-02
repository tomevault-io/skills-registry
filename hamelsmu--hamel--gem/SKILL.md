---
name: gem
description: Multimodal AI processing and image generation using Google Gemini. Use for analyzing PDFs, images, videos, YouTube links, and other large documents. Also generates images with Nano Banana Pro. Ideal when you need to extract information from files that require vision or multimodal understanding, or generate images from text prompts. Use when this capability is needed.
metadata:
  author: hamelsmu
---

# Gemini Multimodal Tool

Use the `ai-gem` CLI tool for multimodal AI processing and image generation via Google's Gemini API.

## Usage

```bash
# Text queries
ai-gem "Write a haiku about Python programming"

# Analyze documents
ai-gem "Summarize this document" document.pdf

# Analyze images
ai-gem "What's in this image?" photo.jpg

# Process YouTube videos
ai-gem "Create a 5-point summary" "https://youtu.be/VIDEO_ID"

# Compare multiple files
ai-gem "Compare these files" file1.pdf file2.png

# Web search
ai-gem "Current AI news" --search

# Generate images (uses Nano Banana Pro by default)
ai-gem --image "A cute robot reading a book in a cozy library"
ai-gem --image "A landscape at sunset" --aspect-ratio 16:9
ai-gem --image "A cat wearing a hat" -o cat.png
ai-gem --image "Edit this to add sunglasses" reference.jpg

# Use alternative image model
ai-gem --image "A blue triangle" -m gemini-2.5-flash-image
```

## Image Generation Options

- `--image` / `-i`: Generate an image instead of text
- `--output` / `-o`: Output file path (auto-generated if omitted)
- `--aspect-ratio` / `-a`: Aspect ratio (1:1, 9:16, 16:9, etc.)
- `--model` / `-m`: Override model (default: nano-banana-pro-preview)
- Attachments serve as reference images for editing

## Requirements

- `GEMINI_API_KEY` environment variable must be set
- The `hamel` package must be installed: `pip install hamel`

## Supported Input Types

- PDFs
- Images (PNG, JPEG, GIF, WebP)
- Videos (MP4, etc.)
- YouTube URLs
- Plain text files
- Multiple files for comparison

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hamelsmu) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
