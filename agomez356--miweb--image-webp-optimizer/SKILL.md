---
name: image-webp-optimizer
description: Optimize and convert images to WebP format with bulk conversion commands and size validation. Use when working with image optimization, WebP conversion, or performance optimization. Use when this capability is needed.
metadata:
  author: agomez356
---

# Image WebP Optimizer

Convert and optimize images to WebP format for the FísicaFans blog to achieve < 200KB target per image and improve page load performance.

## Why WebP?

- **30% smaller** than PNG/JPG at equivalent quality
- **Lossless and lossy** compression support
- **Transparency** support (unlike JPG)
- **Wide browser support** (98%+ as of 2024)

## Optimization Targets

| Image Type | Max Size | Format | Compression |
|------------|----------|--------|-------------|
| Hero images | 200 KB | WebP | 85% quality |
| OG images | 300 KB | PNG/JPG | No WebP (compatibility) |
| Thumbnails | 50 KB | WebP | 80% quality |
| Icons | 10 KB | SVG preferred | N/A |

## Installation

### Install cwebp (WebP converter)

```bash
# macOS
brew install webp

# Ubuntu/Debian
sudo apt-get install webp

# Verify installation
cwebp -version
```

## Single Image Conversion

### Basic Conversion

```bash
# Convert PNG to WebP (auto quality)
cwebp input.png -o output.webp

# Convert JPG to WebP with specific quality (85%)
cwebp -q 85 input.jpg -o output.webp

# Lossless conversion (larger file, perfect quality)
cwebp -lossless input.png -o output.webp
```

### With Size Target

```bash
# Target 200KB file size
cwebp -size 200 input.png -o output.webp

# Target 200KB with quality constraint
cwebp -size 200 -q 90 input.png -o output.webp
```

## Bulk Conversion

### Convert All Images in Directory

```bash
# Create output directory
mkdir -p webp_output

# Convert all PNG files
for file in *.png; do
  cwebp -q 85 "$file" -o "webp_output/${file%.png}.webp"
done

# Convert all JPG files
for file in *.jpg; do
  cwebp -q 85 "$file" -o "webp_output/${file%.jpg}.webp"
done
```

### Recursive Conversion (All Subdirectories)

```bash
# Convert all images in public/images/ recursively
find public/images -type f \( -iname "*.png" -o -iname "*.jpg" -o -iname "*.jpeg" \) -print0 | while IFS= read -r -d '' file; do
  # Get directory and filename
  dir=$(dirname "$file")
  base=$(basename "$file")
  name="${base%.*}"

  # Convert to WebP
  cwebp -q 85 "$file" -o "$dir/$name.webp"

  echo "✓ Converted: $file → $dir/$name.webp"
done
```

## Physics-Specific Optimization

### Hero Images (1200×630px)

```bash
# Optimize hero image for blog posts
cwebp -resize 1200 630 -q 85 input.png -o hero.webp

# Verify size
ls -lh hero.webp | awk '{print $5}'
```

### Physics Visualization Screenshots

```bash
# Optimize Canvas screenshot (maintain aspect ratio)
cwebp -resize 800 0 -q 80 simulation.png -o simulation.webp
```

### Category Icons (Square, 256×256px)

```bash
# Resize and optimize category icons
for icon in thermodynamics quantum classical electromagnetism relativity; do
  cwebp -resize 256 256 -q 85 "$icon.png" -o "$icon-icon.webp"
done
```

## Validation

### Check Image Size

```bash
# Check if image is under 200KB
check_size() {
  size=$(stat -f%z "$1" 2>/dev/null || stat -c%s "$1" 2>/dev/null)
  size_kb=$((size / 1024))

  if [ $size_kb -lt 200 ]; then
    echo "✓ $1: ${size_kb}KB (under 200KB)"
  else
    echo "✗ $1: ${size_kb}KB (exceeds 200KB!)"
  fi
}

# Usage
check_size public/images/hero.webp
```

### Bulk Size Check

```bash
# Check all WebP images in public/images
find public/images -name "*.webp" -type f -exec sh -c '
  for file; do
    size=$(stat -f%z "$file" 2>/dev/null || stat -c%s "$file" 2>/dev/null)
    size_kb=$((size / 1024))
    if [ $size_kb -gt 200 ]; then
      echo "⚠️  $file: ${size_kb}KB"
    fi
  done
' sh {} +
```

## Optimization Script

Create `scripts/optimize-images.sh`:

```bash
#!/bin/bash

# Image Optimization Script for FísicaFans
# Converts all PNG/JPG in public/images/ to WebP

TARGET_DIR="public/images"
QUALITY=85
MAX_SIZE_KB=200

echo "🖼️  Starting image optimization..."
echo "📂 Target directory: $TARGET_DIR"
echo "🎯 Quality: $QUALITY%"
echo "📏 Max size: ${MAX_SIZE_KB}KB"
echo ""

# Check if cwebp is installed
if ! command -v cwebp &> /dev/null; then
  echo "❌ cwebp not found. Install with: brew install webp"
  exit 1
fi

# Counter
converted=0
skipped=0
oversized=0

# Find and convert images
find "$TARGET_DIR" -type f \( -iname "*.png" -o -iname "*.jpg" -o -iname "*.jpeg" \) | while read -r file; do
  dir=$(dirname "$file")
  base=$(basename "$file")
  name="${base%.*}"
  webp_file="$dir/$name.webp"

  # Skip if WebP already exists and is newer
  if [ -f "$webp_file" ] && [ "$webp_file" -nt "$file" ]; then
    echo "⏭️  Skipped (already optimized): $file"
    ((skipped++))
    continue
  fi

  # Convert to WebP
  cwebp -q $QUALITY "$file" -o "$webp_file" -quiet

  # Check size
  size=$(stat -f%z "$webp_file" 2>/dev/null || stat -c%s "$webp_file" 2>/dev/null)
  size_kb=$((size / 1024))

  if [ $size_kb -gt $MAX_SIZE_KB ]; then
    echo "⚠️  Converted (LARGE): $file → $webp_file (${size_kb}KB)"
    ((oversized++))
  else
    echo "✓ Converted: $file → $webp_file (${size_kb}KB)"
  fi

  ((converted++))
done

echo ""
echo "✅ Optimization complete!"
echo "   Converted: $converted"
echo "   Skipped: $skipped"
echo "   Oversized: $oversized"
```

Make executable:
```bash
chmod +x scripts/optimize-images.sh
./scripts/optimize-images.sh
```

## Astro Integration

### Using Astro Image Component

```astro
---
import { Image } from 'astro:assets'
import heroImage from '../assets/hero.png'
---

<!-- Astro auto-generates WebP -->
<Image
  src={heroImage}
  alt="Physics Education Blog"
  width={1200}
  height={630}
  format="webp"
  quality={85}
/>
```

### Manual Picture Element (Fallback)

```html
<picture>
  <source srcset="/images/hero.webp" type="image/webp">
  <img src="/images/hero.jpg" alt="Hero" loading="lazy">
</picture>
```

## Pre-commit Hook (Automatic Optimization)

Create `.husky/pre-commit`:

```bash
#!/bin/sh

# Auto-optimize images before commit
echo "🖼️  Optimizing images..."

# Find staged images
staged_images=$(git diff --cached --name-only --diff-filter=ACM | grep -E '\.(png|jpg|jpeg)$')

if [ -n "$staged_images" ]; then
  for file in $staged_images; do
    if [ -f "$file" ]; then
      dir=$(dirname "$file")
      name="${file%.*}"
      webp_file="$dir/$name.webp"

      # Convert to WebP
      cwebp -q 85 "$file" -o "$webp_file" -quiet 2>/dev/null

      if [ -f "$webp_file" ]; then
        # Stage the WebP file
        git add "$webp_file"
        echo "✓ Generated: $webp_file"
      fi
    fi
  done
fi

echo "✅ Image optimization complete!"
```

## Troubleshooting

### Issue 1: cwebp Not Found

```bash
# macOS
brew install webp

# Linux
sudo apt-get update && sudo apt-get install webp

# Windows (WSL2)
sudo apt-get install webp
```

### Issue 2: Image Still Too Large

```bash
# Try lower quality
cwebp -q 70 input.png -o output.webp

# Or target specific size
cwebp -size 200 input.png -o output.webp

# Or resize dimensions first
cwebp -resize 800 0 -q 85 input.png -o output.webp
```

### Issue 3: Loss of Quality

```bash
# Use higher quality
cwebp -q 95 input.png -o output.webp

# Or lossless (larger file)
cwebp -lossless input.png -o output.webp

# Compare visually
open input.png output.webp
```

## Quality Comparison

| Quality | File Size | Use Case |
|---------|-----------|----------|
| 60-70 | ~50KB | Thumbnails, small images |
| 75-85 | ~100-150KB | General purpose (recommended) |
| 85-95 | ~150-250KB | Hero images, important visuals |
| Lossless | ~300-500KB | Graphics, diagrams (when quality is critical) |

## Testing

### Visual Quality Test

```bash
# Generate different qualities for comparison
for q in 70 80 85 90 95; do
  cwebp -q $q input.png -o "output-q${q}.webp"
done

# List file sizes
ls -lh output-q*.webp
```

### Browser Compatibility Test

```html
<!-- Test WebP support -->
<script>
  const webpSupported = document.createElement('canvas')
    .toDataURL('image/webp')
    .startsWith('data:image/webp')

  console.log('WebP supported:', webpSupported)
</script>
```

## Checklist

Before committing images:

- [ ] All images converted to WebP
- [ ] All WebP files < 200KB (hero images)
- [ ] All WebP files < 50KB (thumbnails)
- [ ] Original PNG/JPG preserved (don't delete)
- [ ] Alt text added in code
- [ ] Fallback formats for OG images (PNG/JPG, not WebP)

## Reference

For bulk conversion script, see [scripts/bulk-convert.sh](scripts/bulk-convert.sh).

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/agomez356) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
