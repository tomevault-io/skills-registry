---
name: apryse-convert
description: Convert documents between formats using Apryse SDK. Convert PDF to Word, Excel, PowerPoint, HTML, EPUB, SVG, images. Convert Office docs and images to PDF. Use when user needs to change document formats. Use when this capability is needed.
metadata:
  author: ipixeldust
---

# Document Conversion

Convert documents between formats. All scripts are in the `scripts/` directory.

## Available Scripts

### Universal Converter
```bash
node scripts/convert.js <input> <output>
```
Auto-detects format from file extensions. Works for most conversions.

### PDF to Word
```bash
node scripts/pdf-to-word.js <input.pdf> <output.docx>
```

### PDF to Excel
```bash
node scripts/pdf-to-excel.js <input.pdf> <output.xlsx>
```
Best results with PDFs containing tables.

### PDF to PowerPoint
```bash
node scripts/pdf-to-pptx.js <input.pdf> <output.pptx>
```

### PDF to HTML
```bash
node scripts/pdf-to-html.js <input.pdf> <output_directory> [--reflow]
```
Use `--reflow` for responsive HTML.

### PDF to EPUB
```bash
node scripts/pdf-to-epub.js <input.pdf> <output.epub> [--title "Book Title"]
```

### PDF to SVG
```bash
node scripts/pdf-to-svg.js <input.pdf> <output_directory> [--per-page]
```
Use `--per-page` to create separate SVG for each page.

### HTML to PDF
```bash
node scripts/html-to-pdf.js <url_or_file.html> <output.pdf> [--paper letter|a4]
```
Accepts URL or local HTML file.

### Images to PDF
```bash
node scripts/images-to-pdf.js <output.pdf> <image1> <image2> ...
node scripts/images-to-pdf.js <output.pdf> --dir <image_directory>
```

## Supported Formats

**To PDF:** docx, xlsx, pptx, html, txt, jpg, png, svg, xps

**From PDF:** docx, xlsx, pptx, html, epub, svg, png, jpg, tiff

## When to Use

- User asks to "convert PDF to Word/Excel/PowerPoint" → appropriate script
- User asks to "convert to PDF" → `convert.js` or `html-to-pdf.js`
- User asks to "make EPUB" or "create ebook" → `pdf-to-epub.js`
- User asks to "convert to SVG" or "vector format" → `pdf-to-svg.js`
- User asks to "combine images into PDF" → `images-to-pdf.js`
- User provides URL and wants PDF → `html-to-pdf.js`

## Notes

- Office conversions (Word, Excel, PowerPoint) may require additional module license
- All scripts require `PDFTRON_LICENSE_KEY` environment variable

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ipixeldust) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
