---
name: pdf
description: Comprehensive PDF manipulation toolkit for extracting text and tables, creating new PDFs, merging/splitting documents, and handling forms. Use when this capability is needed.
metadata:
  author: salomonsv81
---

# PDF Processing Guide

## Quick Start

```python
from pypdf import PdfReader, PdfWriter

# Read a PDF
reader = PdfReader("document.pdf")
print(f"Pages: {len(reader.pages)}")

# Extract text
text = ""
for page in reader.pages:
    text += page.extract_text()
```

## Python Libraries

### pypdf - Basic Operations
- Merge, split, rotate PDFs
- Extract metadata
- Password protection

### pdfplumber - Text and Table Extraction
```python
import pdfplumber

with pdfplumber.open("document.pdf") as pdf:
    for page in pdf.pages:
        text = page.extract_text()
        tables = page.extract_tables()
```

### reportlab - Create PDFs
```python
from reportlab.lib.pagesizes import letter
from reportlab.pdfgen import canvas

c = canvas.Canvas("hello.pdf", pagesize=letter)
c.drawString(100, 750, "Hello World!")
c.save()
```

## Command-Line Tools

```bash
# Extract text (poppler-utils)
pdftotext -layout input.pdf output.txt

# Merge PDFs (qpdf)
qpdf --empty --pages file1.pdf file2.pdf -- merged.pdf
```

## Quick Reference

| Task | Best Tool |
|------|----------|
| Merge PDFs | pypdf |
| Extract text | pdfplumber |
| Extract tables | pdfplumber |
| Create PDFs | reportlab |
| OCR scanned PDFs | pytesseract |
| Fill PDF forms | pypdf or pdf-lib |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/salomonsv81) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
