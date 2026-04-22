---
name: researchimage-analyze
description: Analyze or compare images and PDFs using AI (Gemini) or local processing (sharp/pixelmatch). Use when performing design review, screenshot comparison, visual diff, edge detection, image metadata inspection, or PDF document analysis. Use when this capability is needed.
metadata:
  author: masseater
---

# Image Analyze

Analyze or compare images and PDF documents. Two scripts available:

- **image-analyze.ts** - AI-powered analysis via Gemini API
- **image-process.ts** - Local image processing (API不要, offline)

## Scripts

Located at `${CLAUDE_PLUGIN_ROOT}/skills/image-analyze/scripts/`.

| Script             | Description                                   | Requires API |
| ------------------ | --------------------------------------------- | :----------: |
| `image-analyze.ts` | AI analysis / comparison (images + PDF)       | Yes (Gemini) |
| `image-process.ts` | Pixel diff, edge, overlay, side-by-side, info |      No      |

---

### image-analyze.ts

AI-powered image analysis using Gemini API.

Supported formats: PNG, JPEG, GIF, WebP, PDF

| Argument         | Required | Description                                     |
| ---------------- | -------- | ----------------------------------------------- |
| `image1`         | ✓        | First image/PDF path                            |
| `image2`         |          | Second image/PDF path (enables comparison mode) |
| `--prompt`, `-p` |          | Custom prompt for analysis/comparison           |

**Analyze single image:**

```bash
${CLAUDE_PLUGIN_ROOT}/skills/image-analyze/scripts/image-analyze.ts ./screenshot.png
```

**Analyze PDF document:**

```bash
${CLAUDE_PLUGIN_ROOT}/skills/image-analyze/scripts/image-analyze.ts ./document.pdf
```

**Compare two images:**

```bash
${CLAUDE_PLUGIN_ROOT}/skills/image-analyze/scripts/image-analyze.ts ./before.png ./after.png
```

**Compare two PDFs:**

```bash
${CLAUDE_PLUGIN_ROOT}/skills/image-analyze/scripts/image-analyze.ts ./v1.pdf ./v2.pdf
```

**With custom prompt:**

```bash
${CLAUDE_PLUGIN_ROOT}/skills/image-analyze/scripts/image-analyze.ts ./design.png -p "Check if this follows Material Design guidelines"
```

---

### image-process.ts

Local image processing tool. No API key required.

#### Subcommands

| Subcommand     | Description                            |
| -------------- | -------------------------------------- |
| `diff`         | Pixel-level diff between two images    |
| `edge`         | Edge detection (Sobel filter)          |
| `overlay`      | Semi-transparent overlay of two images |
| `side-by-side` | Horizontal side-by-side comparison     |
| `info`         | Display image metadata                 |

#### diff

Compare two same-size images pixel by pixel. Outputs a diff image and match rate.

| Option        | Required | Default             | Description                           |
| ------------- | :------: | ------------------- | ------------------------------------- |
| `--image1`    |    ✓     |                     | Source image                          |
| `--image2`    |    ✓     |                     | Target image                          |
| `--output`    |          | `{image1}_diff.png` | Output path                           |
| `--threshold` |          | `0.1`               | Sensitivity (0-1, smaller = stricter) |

```bash
${CLAUDE_PLUGIN_ROOT}/skills/image-analyze/scripts/image-process.ts diff --image1 before.png --image2 after.png --threshold 0.05
```

#### edge

Extract edges using Sobel filter. Useful for comparing layout structure.

| Option     | Required | Default            | Description |
| ---------- | :------: | ------------------ | ----------- |
| `--image`  |    ✓     |                    | Input image |
| `--output` |          | `{image}_edge.png` | Output path |

```bash
${CLAUDE_PLUGIN_ROOT}/skills/image-analyze/scripts/image-process.ts edge --image screenshot.png
```

#### overlay

Overlay two images with adjustable opacity to visually spot differences.

| Option      | Required | Default                | Description           |
| ----------- | :------: | ---------------------- | --------------------- |
| `--image1`  |    ✓     |                        | Base image            |
| `--image2`  |    ✓     |                        | Overlay image         |
| `--output`  |          | `{image1}_overlay.png` | Output path           |
| `--opacity` |          | `0.5`                  | Overlay opacity (0-1) |

```bash
${CLAUDE_PLUGIN_ROOT}/skills/image-analyze/scripts/image-process.ts overlay --image1 before.png --image2 after.png --opacity 0.3
```

#### side-by-side

Place two images side by side for visual comparison.

| Option     | Required | Default                   | Description             |
| ---------- | :------: | ------------------------- | ----------------------- |
| `--image1` |    ✓     |                           | Left image              |
| `--image2` |    ✓     |                           | Right image             |
| `--output` |          | `{image1}_sidebyside.png` | Output path             |
| `--gap`    |          | `4`                       | Gap between images (px) |

```bash
${CLAUDE_PLUGIN_ROOT}/skills/image-analyze/scripts/image-process.ts side-by-side --image1 before.png --image2 after.png --gap 8
```

#### info

Display image metadata (dimensions, format, color space, file size, DPI, alpha).

| Option    | Required | Default | Description |
| --------- | :------: | ------- | ----------- |
| `--image` |    ✓     |         | Input image |

```bash
${CLAUDE_PLUGIN_ROOT}/skills/image-analyze/scripts/image-process.ts info --image screenshot.png
```

---

## Use Cases

- Design review and feedback (AI analysis)
- PDF document analysis and comparison (AI analysis)
- Screenshot comparison for UI testing (diff, side-by-side)
- Visual diff between design iterations (diff, overlay)
- Layout structure comparison (edge detection)
- Image metadata inspection (info)
- Accessibility analysis of UI elements (AI analysis)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/masseater) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
