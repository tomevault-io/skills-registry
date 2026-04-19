---
name: emoji-extractor
description: Extracts emojis as SVGs from a given font file or URL. Use this skill when you need to obtain high-quality SVG assets for specific emojis from a specific font.
metadata:
  author: d954mas
---

# Emoji Extractor

## Overview

This skill provides a workflow and tools to extract emoji glyphs from font files and save them as standalone SVG files. It uses a Node.js script to parse the font and generate the SVG path.

## When to Use

- You need an SVG version of a specific emoji.
- You have a font file (or URL to one) and want to extract one or more glyphs from it.
- **Note:** Works best with standard outline fonts (e.g. NotoEmoji-Regular). Color bitmap fonts (like NotoColorEmoji) might not be fully supported or may fail to yield vector paths.
- You want consistent emoji assets across different platforms by embedding them as SVGs.

## Usage

### Prerequisites
- Node.js installed.
- Dependencies installed in the `scripts` directory (run `npm install` in `.agent/skills/emoji-extractor/scripts`).

### Extraction Command

Use the `extract_emoji.js` script to extract emojis.

```bash
node .agent/skills/emoji-extractor/scripts/extract_emoji.js --font <path_to_font> --emoji <emoji_list> --out <output_file_or_dir> [--force]
```

**Arguments:**
- `--font`: Path to local font OR URL. **URL fonts are cached** in `tmp/fonts_cache/` to avoid repeated downloads.
- `--emoji`: Comma-separated list of emojis (e.g. "🔥, 🚀") or unicode hex codes.
- `--out`: Output directory (recommended for batch) or file path.
- `--force`: Overwrite existing files (default: skip if exists).

### Examples

**Extract 'Fire' emoji from local Noto font:**
```bash
node .agent/skills/emoji-extractor/scripts/extract_emoji.js --font "assets/fonts/NotoColorEmoji.ttf" --emoji "🔥" --out "assets/emoji/fire.svg"
```

**Extract 'Rocket' emoji from URL:**
```bash
node .agent/skills/emoji-extractor/scripts/extract_emoji.js --font "https://example.com/fonts/NotoEmoji-Regular.ttf" --emoji "🚀" --out "assets/emoji/"
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/d954mas) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
