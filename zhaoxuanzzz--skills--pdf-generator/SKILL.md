---
name: pdf-generator
description: Read and generate PDF files. Use this skill when you need to extract text from PDFs or create new PDF documents from text or markdown content. Use when this capability is needed.
metadata:
  author: zhaoxuanzzz
---

# PDF Generator & Reader

This skill provides tools to read text from PDF files and generate new PDFs from text content.

## Dependencies

- python3
- reportlab
- pdfplumber

## Usage

### Reading PDFs

To extract text from a PDF file:

```bash
python3 scripts/read_pdf.py <path_to_pdf>
```

### Generating PDFs

To generate a PDF from a text file:

```bash
python3 scripts/generate_pdf.py <input_text_file> <output_pdf_file>
```

## Scripts

- `scripts/read_pdf.py`: Extracts text from a PDF file.
- `scripts/generate_pdf.py`: Converts a text file to a PDF file.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/zhaoxuanzzz) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
