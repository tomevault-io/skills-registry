---
name: nano-banana-antigravity
description: Generate or edit images via Nano Banana Pro using Antigravity OAuth (no API key needed!) Use when this capability is needed.
metadata:
  author: duclm1x1
---

# Nano Banana Antigravity (Gemini 3 Pro Image via OAuth)

Generate images using Nano Banana Pro (Gemini 3 Pro Image) via your existing Google Antigravity OAuth credentials.

**No separate API key needed!** Uses the same OAuth tokens as your OpenClaw Antigravity provider.

## Generate Image

**For WhatsApp HD (recommended):**
```bash
{baseDir}/scripts/generate_whatsapp_hd.sh \
  --prompt "your image description" \
  --filename "output.jpg" \
  --aspect-ratio 16:9 \
  --resolution 4K
```

**Standard PNG output:**
```bash
uv run {baseDir}/scripts/generate_image.py --prompt "your image description" --filename "output.png"
```

## Generate with Options

```bash
{baseDir}/scripts/generate_whatsapp_hd.sh \
  --prompt "a sunset over mountains" \
  --filename "sunset.jpg" \
  --aspect-ratio 16:9 \
  --resolution 4K
```

**What `generate_whatsapp_hd.sh` does:**
- ✅ Auto-converts PNG → progressive JPEG
- ✅ Optimizes quality (85-92%) to stay under 6.28MB
- ✅ WhatsApp HD ready (no compression!)
- ✅ Warns if image is too large

## Edit/Composite Images

```bash
uv run {baseDir}/scripts/generate_image.py \
  --prompt "add sunglasses to this person" \
  --filename "edited.png" \
  -i original.png
```

## Multi-image Composition

```bash
uv run {baseDir}/scripts/generate_image.py \
  --prompt "combine these into one scene" \
  --filename "composite.png" \
  -i image1.png -i image2.png -i image3.png
```

## Options

- `--prompt, -p` (required): Image description or edit instructions
- `--filename, -f` (required): Output filename
- `--input-image, -i`: Input image(s) for editing (can be repeated)
- `--aspect-ratio, -a`: 1:1 (default), 2:3, 3:2, 3:4, 4:3, 4:5, 5:4, 9:16, 16:9, 21:9
- `--resolution, -r`: 1K, 2K (default), 4K

## Authentication

Uses existing OpenClaw Antigravity OAuth credentials. Make sure you're authenticated:

```bash
openclaw models auth login --provider google-antigravity
```

The script looks for credentials in:
- `~/.openclaw/credentials/google-antigravity.json`
- `~/.config/openclaw/credentials/google-antigravity.json`
- `~/.config/opencode/antigravity-accounts.json`

## WhatsApp HD Upload Limits

**For best WhatsApp HD quality:**
- Use `generate_whatsapp_hd.sh` instead of `generate_image.py`
- Output filename must end in `.jpg` or `.jpeg`
- Images ≤6.28MB will upload without compression
- Images >6.28MB may be compressed by WhatsApp

**Size guidelines:**
- ≤6.28MB → ✅ HD (no compression)
- 6.29-6.5MB → Slight compression (~5.7MB)
- 6.5-7.6MB → Moderate compression (~6.2MB)
- >9MB → ⚠️ Heavy compression

## Notes

- The script prints a `MEDIA:` line for OpenClaw to auto-attach on supported chat providers.
- Do not read the image back; report the saved path only.
- Uses timestamps in filenames for uniqueness: `yyyy-mm-dd-hh-mm-ss-name.png`
- Falls back to regular Nano Banana if Nano Banana Pro isn't available yet.
- **Account rotation:** Automatically tries all 12 Antigravity accounts on rate limits

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/duclm1x1) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
