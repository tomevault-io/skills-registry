---
name: feishu-sticker
description: Send images as native Feishu stickers. Features auto-upload, caching, and GIF-to-WebP conversion. Use when this capability is needed.
metadata:
  author: duclm1x1
---

# Feishu Sticker Skill

Sends a sticker (image) to a Feishu user or group.
Automatically uploads the image to Feishu (caching the `image_key` via MD5), converts GIFs to WebP for efficiency, and supports smart search.

## Features
- **Auto-Upload**: Uploads local images to Feishu CDN on demand.
- **Caching**: Caches `image_key` by file hash to avoid re-uploading.
- **Optimization**: Auto-converts GIFs to WebP (via `ffmpeg-static`) and compresses large images (>5MB).
- **Smart Search**: Find stickers by `--query` or `--emotion`.

## Usage

```bash
# Send random sticker
node skills/feishu-sticker/send.js --target "ou_..."

# Send specific file
node skills/feishu-sticker/send.js --target "ou_..." --file "/path/to/image.jpg"

# Search and send
node skills/feishu-sticker/send.js --target "ou_..." --query "angry cat"
node skills/feishu-sticker/send.js --target "ou_..." --emotion "happy"
```

## Setup
1.  Put your stickers in `~/.openclaw/media/stickers/` (or set `STICKER_DIR`).
2.  Install dependencies: `npm install` (requires `axios`, `commander`, `ffmpeg-static`, `form-data`, `dotenv`).

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/duclm1x1) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
