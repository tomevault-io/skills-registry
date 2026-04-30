---
name: zimage-skill
description: Generate images using ModelScope Z-Image-Turbo API. Use when user asks to generate, create, or make images, pictures, or illustrations. Use when this capability is needed.
metadata:
  author: aiskillstore
---

# Z-Image Generation

Generate images using ModelScope's Tongyi-MAI/Z-Image-Turbo model.

## Requirements

```bash
pip install requests Pillow
```

## Usage

```bash
python3 ~/.claude/skills/zimage-skill/generate.py "prompt" [output_path]
```

## Environment

Optional: Set `MODELSCOPE_API_KEY` to use your own API key.

## Examples

```bash
python3 ~/.claude/skills/zimage-skill/generate.py "A golden cat" cat.jpg
python3 ~/.claude/skills/zimage-skill/generate.py "Sunset over mountains"
```

## Notes

- Default output: `result_image.jpg`
- Timeout: 120 seconds max
- Supports Chinese and English prompts

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/aiskillstore) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
