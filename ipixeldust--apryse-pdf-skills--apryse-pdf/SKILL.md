---
name: apryse-pdf
description: PDF manipulation using Apryse SDK. Extract text, merge/split documents, fill forms, add watermarks, optimize file size, convert to images. Use when the user needs to process, combine, or manipulate PDF files. Use when this capability is needed.
metadata:
  author: ipixeldust
---

# Apryse PDF Operations

Use these scripts to manipulate PDF files. All scripts are in the `scripts/` directory.

## Available Scripts

### Extract Text
```bash
node scripts/extract-text.js <input.pdf> [output.txt]
```
Extracts all text from a PDF. If no output file specified, prints to stdout.

### Merge PDFs
```bash
node scripts/merge-pdfs.js <output.pdf> <input1.pdf> <input2.pdf> [input3.pdf ...]
```
Combines multiple PDFs into one document.

### Split PDF
```bash
node scripts/split-pdf.js <input.pdf> <output_directory> [--range 1-5,8,10-12]
```
Splits a PDF into individual page files. Use `--range` to extract specific pages.

### Convert to Images
```bash
node scripts/pdf-to-images.js <input.pdf> <output_directory> [--format png|jpg] [--dpi 150]
```
Converts each page to an image file.

### Fill Form
```bash
# List available fields:
node scripts/fill-form.js <input.pdf> --list

# Fill fields from JSON:
node scripts/fill-form.js <input.pdf> <output.pdf> <values.json>
```
The values.json format:
```json
{"fieldName": "value", "checkboxField": true}
```

### Add Watermark
```bash
node scripts/add-watermark.js <input.pdf> <output.pdf> "WATERMARK TEXT" [--opacity 0.3] [--angle 45]
```
Adds diagonal text watermark to all pages.

### Optimize (Reduce Size)
```bash
node scripts/optimize-pdf.js <input.pdf> <output.pdf> [--quality 1-10]
```
Compresses images and removes unused data. Quality 1=smallest, 10=highest.

## When to Use

- User asks to "extract text" or "get text from PDF" → `extract-text.js`
- User asks to "merge", "combine", or "join" PDFs → `merge-pdfs.js`
- User asks to "split" or "separate pages" → `split-pdf.js`
- User asks to "convert to images" or "make PNG/JPG" → `pdf-to-images.js`
- User asks to "fill form" or "fill out PDF" → `fill-form.js`
- User asks to "add watermark" or "stamp" → `add-watermark.js`
- User asks to "compress", "optimize", or "reduce size" → `optimize-pdf.js`

## Notes

- All scripts require `PDFTRON_LICENSE_KEY` environment variable
- Scripts output progress to stderr and results to stdout
- Use absolute paths for reliability

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ipixeldust) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
