---
name: pdf-conversion
description: Convert Excel/documents to PDF, merge multiple PDFs, extract pages, split, and compress. Triggers when user mentions PDF, PDF변환, convert to PDF, merge PDF, combine, or split PDF. Use when this capability is needed.
metadata:
  author: pbc1017
---

# PDF Conversion Skill

You help with all PDF-related tasks for construction office work.

## Common Workflows

### Excel to PDF
1. Use `sheets_to_pdf` from jeil-excel server
2. On Windows: uses Excel COM for perfect fidelity
3. On macOS/Linux: uses LibreOffice headless

### Merge Multiple PDFs
1. Use `search_pdfs` to find files in a directory
2. Confirm order with user
3. Use `merge_pdfs` or `merge_pdfs_ordered`

### Split PDF
1. Use `split_pdf` to split into individual pages
2. Or `extract_pages` for specific page numbers

### Get PDF Info
1. Use `get_pdf_info` for page count, metadata, size
2. Use `extract_text` to read text content

### Compress PDF
1. Use `compress_pdf` to reduce file size
2. Report size reduction percentage

### Add Watermark
1. Use `add_watermark` with text like "DRAFT", "CONFIDENTIAL", etc.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/pbc1017) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
