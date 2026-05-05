---
name: generating-memes
description: Creates memes using the meme CLI with 298 templates. Generates, previews, searches, and lists meme templates. Use when user asks to make memes, create memes, generate memes, or mentions specific meme names like petpet, slap, hug, rub, etc.
metadata:
  author: neversight
---

# Generating Memes

Creates memes using the meme CLI tool with 298+ templates.

## Quick Start

List all templates:
```bash
meme list
```

Search templates by keyword:
```bash
meme search <keyword>
```

Generate a meme:
```bash
meme generate <template> --images <paths> --texts <texts>
```

## Popular Templates

Most commonly used templates:

| Template | Description | Type |
|----------|-------------|------|
| `petpet` | Petting animation (摸/摸摸) | Image |
| `slap` | Slapping animation (一巴掌) | Image |
| `hug` | Hugging animation (抱/抱抱) | Image |
| `rub` | Nuzzling animation (贴/贴贴) | Image |
| `pat` | Patting animation (拍) | Image |
| `kiss` | Kissing animation (亲/亲亲) | Image |
| `pinch` | Pinching face (捏/捏脸) | Image |
| `5000choyen` | Big/small text contrast | Text |
| `always` | "Always" format meme | Text |
| `shock` | Shocked reaction (震惊) | Text |
| `clown` | Clown meme (小丑) | Image |
| `stare_at_you` | Staring at you (盯着你) | Image |
| `loading` | Loading animation | Text |
| `good_news` | Good news header (喜报) | Text |
| `bad_news` | Bad news header (悲报) | Text |
| `applaud` | Applause (鼓掌) | Image |
| `praise` | Praise (表扬) | Text |
| `speechless` | Speechless (无语) | Image |
| `run_away` | Run away (快逃) | Image |
| `suck` | Suck/Sip animation (吸/嗦) | Image |

[See full template list](references/templates.md)

## Usage Patterns

### Image-based Memes

Templates requiring one or more images:
```bash
# Single image
meme generate petpet --images /path/to/photo.jpg

# Save to file
meme generate petpet --images /path/to/photo.jpg > output.gif
```

### Text-based Memes

Templates using only text:
```bash
# 5000兆 (big/small contrast)
meme generate 5000choyen --texts "IMPORTANT" "ignore this"

# Always meme
meme generate always --texts "the answer is 42"
```

### Mixed (Images + Text)

### Recommended Workflow

1. **Search** for a template: `meme search <keyword>`
2. **Preview** the template: `meme preview <template>`
3. **Check** requirements: `meme info <template>`
4. **Generate** the meme: `meme generate <template> [options]`

### Example: Create a Petpet Meme

```bash
# 1. Verify template exists
meme search pet

# 2. See what it needs
meme info petpet
# Output: needs 1 image, 0 text

# 3. Generate
meme generate petpet --images friend.jpg > petpet.gif
```

## Commands Reference

| Command | Description |
|---------|-------------|
| `meme list` | List all 298 templates |
| `meme search <keyword>` | Search templates by keyword |
| `meme info <template>` | Show template requirements (images, texts, params) |
| `meme preview <template>` | Generate template preview |
| `meme generate <template>` | Create meme |
| `meme download` | Download required resources |

[See more examples](references/examples.md)

## Troubleshooting

### "meme: command not found"

The meme CLI is not installed. Install it from GitHub:

```bash
# Download the binary
curl -L https://github.com/MemeCrafters/meme-generator-rs/releases/latest/download/meme-x86_64-unknown-linux-gnu -o meme

# Make executable and install
chmod +x meme
sudo mv meme /usr/local/bin/

# Download required resources
meme download
```

**GitHub**: https://github.com/MemeCrafters/meme-generator-rs

**Alternative**: Build from source with Rust:
```bash
cargo install meme-generator
meme download
```

### Template Not Found

If generation fails with "unknown template" error:

```bash
# Verify template name
meme list | grep <template>

# Search for similar templates
meme search <keyword>

# Check template info
meme info <template>
```

### Missing Resources

If images or templates are missing:

```bash
meme download
```

This downloads all required template assets.

### Network Issues (Download Failed)

If `meme download` fails with connection timeout:

```bash
# Error example:
# WARN Failed to download: Connection timed out (os error 110)
# The CLI cannot reach cdn.jsdelivr.net

# Solution: Download resources manually from GitHub releases
# Visit: https://github.com/MemeCrafters/meme-generator-rs/releases
```

**Note**: Some templates may work without downloaded resources if they have built-in assets.

## Tips

- Use `meme info <template>` before generating to understand requirements
- Redirect output to save: `> output.gif`
- Many templates support both images and text
- Some templates have optional parameters (like `--circle` for petpet)
- Use `meme search` for discovery when unsure of template name

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neversight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
