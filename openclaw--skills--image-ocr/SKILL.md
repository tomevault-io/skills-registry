---
name: image-ocr
description: Extract text from images using Tesseract OCR Use when this capability is needed.
metadata:
  author: openclaw
---

# Image OCR

Extract text from images using Tesseract OCR. Supports multiple languages and image formats including PNG, JPEG, TIFF, and BMP.

## Commands

```bash
# Extract text from an image (default: English)
image-ocr "screenshot.png"

# Extract text with a specific language
image-ocr "document.jpg" --lang eng
```

## Install

```bash
sudo dnf install tesseract
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
