---
name: trace-to-svg
description: Trace bitmap images (PNG/JPG/WebP) into clean SVG paths using potrace/mkbitmap. Use to convert logos/silhouettes into vectors for downstream CAD workflows (e.g., create-dxf etch_svg_path) and for turning reference images into manufacturable outlines. Use when this capability is needed.
metadata:
  author: thinkfleetai
---

# trace-to-svg

Convert a bitmap into a vector SVG using `mkbitmap` + `potrace`.

## Quick start

```bash
# 1) Produce a silhouette-friendly SVG
bash scripts/trace_to_svg.sh input.png --out out.svg

# 2) Higher contrast + less noise
bash scripts/trace_to_svg.sh input.png --out out.svg --threshold 0.6 --turdsize 20

# 3) Feed into create-dxf (example)
# - set create-dxf drawing.etch_svg_paths[].d to the SVG path `d` you want, or
# - store the traced SVG and reference it in your pipeline.
```

## Notes

- This is best for **logos, silhouettes, high-contrast shapes**.
- For photos or complex shading, results depend heavily on thresholding.
- Output is usually one or more `<path>` elements.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/thinkfleetai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
