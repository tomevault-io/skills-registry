---
name: unsplash-image-finder
description: Use this skill when you need to find and optimize images from Unsplash for web pages. This skill searches for images using the Unsplash API and provides optimized URLs with appropriate parameters.
metadata:
  author: toiee-lab
---

# Unsplash Image Finder

This skill helps find and optimize images from Unsplash for web content.

## When to Use

Invoke this skill when:
- Creating web pages that need hero images, thumbnails, or content images
- User requests images for their website but doesn't provide specific URLs
- Building landing pages, blog posts, or documentation with visual content

## Search Command

Use the existing search script:

```bash
node dev-tools/unsplash-search.js "search keyword"
```

### Single Keyword Search
```bash
node dev-tools/unsplash-search.js "coffee shop"
```

### Multiple Keywords Search
```bash
node dev-tools/unsplash-search.js "technology,innovation,startup"
```

## URL Optimization Parameters

Unsplash URLs should be optimized with the following parameters:

| Parameter | Description | Values |
|-----------|-------------|--------|
| `w` | Width in pixels | 400, 800, 1200, 1600, 1920 |
| `q` | Quality (1-100) | 80 (recommended) |
| `fm` | Format | webp (recommended), jpg, png |
| `fit` | Fit mode | crop (recommended) |

### Recommended Width by Usage

| Usage | Width | Example |
|-------|-------|---------|
| Thumbnail | 400 | Cards, small previews |
| Content image | 800 | Blog posts, documentation |
| Large image | 1200 | Feature sections |
| Hero section | 1600 | Above-the-fold heroes |
| Full-width background | 1920 | Full-screen backgrounds |

### Optimized URL Format

```
https://images.unsplash.com/photo-[ID]?ixid=[ID]&ixlib=rb-4.1.0&w=[WIDTH]&q=80&fm=webp&fit=crop
```

## HTML Implementation

When inserting images, always include:

```html
<img
  src="[optimized-url]"
  alt="Descriptive alt text"
  loading="lazy"
  decoding="async"
  class="[responsive-classes]"
>
```

### Required Attributes
- `alt`: Descriptive text for accessibility
- `loading="lazy"`: Defer loading for off-screen images
- `decoding="async"`: Non-blocking image decode

## Fallback Protocol

If the search fails, execute in order:

1. **Primary**: Try with original keyword
2. **Secondary**: Try broader/simpler keyword (e.g., "coffee" instead of "artisanal coffee shop interior")
3. **Tertiary**: Ask user for specific image URL or preference
4. **NEVER**: Guess or hallucinate Unsplash URLs from training data

## Error Handling

If the search script returns an error:
1. Check that `UNSPLASH_ACCESS_KEY` is set in `.env.local` or environment
2. Try with a different/simpler keyword
3. Report the error to the user

## Environment Setup

The search script requires:
- Node.js
- `UNSPLASH_ACCESS_KEY` environment variable

See `.env.local.example` for reference.

## Example Workflow

1. **Receive request**: "Add a hero image for a tech startup page"
2. **Search**: `node dev-tools/unsplash-search.js "tech startup office"`
3. **Get result**: Script returns optimized URL
4. **Apply to code**:
   ```html
   <img
     src="https://images.unsplash.com/photo-xxx?w=1600&q=80&fm=webp&fit=crop"
     alt="Modern tech startup office with team collaboration"
     loading="lazy"
     decoding="async"
     class="w-full h-auto object-cover"
   >
   ```

## Important Notes

- This skill ONLY searches and provides URLs - it does NOT modify files
- File modifications are handled by Claude Code main conversation
- Always verify URLs work before using them
- Respect Unsplash API usage guidelines

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/toiee-lab) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
