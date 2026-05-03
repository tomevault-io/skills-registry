---
name: processing-pdfs
description: Extract text, tables, and metadata from PDF files. Fill forms, merge documents, and convert to other formats. Use when working with PDF files or when the user mentions PDFs, forms, document extraction, or page manipulation. Use when this capability is needed.
metadata:
  author: astroair
---

# Processing PDFs

Extract text and tables from PDF files, fill forms, and merge documents.

## Quick Start

Extract text with pdfplumber:

```python
import pdfplumber

with pdfplumber.open("document.pdf") as pdf:
    for page in pdf.pages:
        text = page.extract_text()
        print(text)
```

## Features

- **Text extraction**: Extract text from any page
- **Table extraction**: Extract tables as structured data
- **Form filling**: Fill PDF forms programmatically
- **Document merging**: Combine multiple PDFs
- **Page manipulation**: Split, rotate, and reorder pages
- **Metadata access**: Read and modify PDF metadata

## Common Operations

### Extract All Text

```python
def extract_all_text(pdf_path):
    """Extract all text from a PDF file."""
    import pdfplumber
    
    text_parts = []
    with pdfplumber.open(pdf_path) as pdf:
        for page in pdf.pages:
            text = page.extract_text()
            if text:
                text_parts.append(text)
    
    return "\n\n".join(text_parts)
```

### Extract Tables

```python
def extract_tables(pdf_path):
    """Extract all tables from a PDF file."""
    import pdfplumber
    
    all_tables = []
    with pdfplumber.open(pdf_path) as pdf:
        for page in pdf.pages:
            tables = page.extract_tables()
            all_tables.extend(tables)
    
    return all_tables
```

### Merge PDFs

```python
def merge_pdfs(pdf_paths, output_path):
    """Merge multiple PDFs into one."""
    from PyPDF2 import PdfMerger
    
    merger = PdfMerger()
    for path in pdf_paths:
        merger.append(path)
    
    merger.write(output_path)
    merger.close()
```

## Configuration

| Option | Type | Default | Description |
|--------|------|---------|-------------|
| `extract_images` | bool | false | Also extract images from PDFs |
| `ocr_enabled` | bool | false | Use OCR for scanned documents |
| `table_strategy` | string | "lines" | Table detection strategy |

## Dependencies

- `pdfplumber` - Text and table extraction
- `PyPDF2` - PDF manipulation
- `pdf2image` - Convert PDF to images (optional)
- `pytesseract` - OCR support (optional)

## API Reference

See [REFERENCE.md](REFERENCE.md) for detailed API documentation.

## Examples

See [EXAMPLES.md](EXAMPLES.md) for more usage examples.

## Troubleshooting

### Text extraction returns empty

1. Check if the PDF contains actual text (not scanned images)
2. Enable OCR for scanned documents: `ocr_enabled: true`
3. Try different extraction settings

### Tables not detected correctly

1. Use `table_strategy: "text"` for text-based tables
2. Adjust table detection settings
3. Consider manual boundary specification

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/astroair) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
