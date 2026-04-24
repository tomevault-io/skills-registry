---
name: image-optimizer
description: Converts img to Next/Image, suggests formats/sizes/placeholder. Use when image imports or static assets are added, or user says "optimize images".
metadata:
  author: mouayadakel
---

# Image Optimizer

## When to Trigger

- Image imports detected
- Static assets added
- "Optimize images"

## What to Do

1. **Next/Image**: Replace <img> with next/image; set width/height or fill; add alt.
2. **Priority**: priority for above-the-fold; lazy for below.
3. **Format**: Prefer WebP; generate responsive sizes if needed; optional blur placeholder.
4. **Size**: Resize large originals; use CDN loader if project has one (e.g. Cloudinary).

Keep originals in version control or asset store; generate optimized variants in build or on upload. Never strip alt for meaningful images.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mouayadakel) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
