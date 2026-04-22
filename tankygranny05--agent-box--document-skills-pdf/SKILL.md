---
name: document-skillspdf
description: Comprehensive PDF manipulation toolkit for extracting text and tables, creating new PDFs, merging/splitting documents, and handling forms. Use when programmatically processing, generating, or analyzing PDF documents. Use when this capability is needed.
metadata:
  author: tankygranny05
---

# PDF Document Skills

## Overview

This skill provides comprehensive PDF manipulation capabilities using Python libraries: PyPDF2 for basic operations, pdfplumber for text extraction, and reportlab for PDF creation.

## When to Use

- Extracting text and tables from PDFs
- Creating new PDF documents
- Merging or splitting PDF files
- Filling PDF forms
- Adding watermarks or annotations
- Converting content to PDF format

## Dependencies

```bash
pip install PyPDF2 pdfplumber reportlab pypdf
```

## Quick Reference

### Extract text from PDF

```python
import pdfplumber

with pdfplumber.open('input.pdf') as pdf:
    for page in pdf.pages:
        text = page.extract_text()
        print(text)
```

### Extract tables from PDF

```python
import pdfplumber

with pdfplumber.open('input.pdf') as pdf:
    for page in pdf.pages:
        tables = page.extract_tables()
        for table in tables:
            for row in table:
                print(row)
```

### Merge PDFs

```python
from PyPDF2 import PdfMerger

merger = PdfMerger()
merger.append('file1.pdf')
merger.append('file2.pdf')
merger.append('file3.pdf')
merger.write('merged.pdf')
merger.close()
```

### Split PDF

```python
from PyPDF2 import PdfReader, PdfWriter

reader = PdfReader('input.pdf')

# Extract specific pages
writer = PdfWriter()
writer.add_page(reader.pages[0])  # First page
writer.add_page(reader.pages[2])  # Third page

with open('extracted.pdf', 'wb') as output:
    writer.write(output)
```

### Create PDF with reportlab

```python
from reportlab.lib.pagesizes import letter
from reportlab.pdfgen import canvas
from reportlab.lib.units import inch

c = canvas.Canvas('output.pdf', pagesize=letter)
width, height = letter

# Add text
c.setFont('Helvetica-Bold', 24)
c.drawString(1*inch, height - 1*inch, 'Document Title')

c.setFont('Helvetica', 12)
c.drawString(1*inch, height - 2*inch, 'This is paragraph text.')

# Add line
c.line(1*inch, height - 2.5*inch, 7.5*inch, height - 2.5*inch)

c.save()
```

### Create PDF with tables (reportlab)

```python
from reportlab.lib.pagesizes import letter
from reportlab.platypus import SimpleDocTemplate, Table, TableStyle
from reportlab.lib import colors

doc = SimpleDocTemplate('output.pdf', pagesize=letter)
elements = []

data = [
    ['Header 1', 'Header 2', 'Header 3'],
    ['Row 1 Col 1', 'Row 1 Col 2', 'Row 1 Col 3'],
    ['Row 2 Col 1', 'Row 2 Col 2', 'Row 2 Col 3'],
]

table = Table(data)
table.setStyle(TableStyle([
    ('BACKGROUND', (0, 0), (-1, 0), colors.grey),
    ('TEXTCOLOR', (0, 0), (-1, 0), colors.whitesmoke),
    ('ALIGN', (0, 0), (-1, -1), 'CENTER'),
    ('FONTNAME', (0, 0), (-1, 0), 'Helvetica-Bold'),
    ('FONTSIZE', (0, 0), (-1, 0), 14),
    ('BOTTOMPADDING', (0, 0), (-1, 0), 12),
    ('BACKGROUND', (0, 1), (-1, -1), colors.beige),
    ('GRID', (0, 0), (-1, -1), 1, colors.black),
]))

elements.append(table)
doc.build(elements)
```

### Add watermark

```python
from PyPDF2 import PdfReader, PdfWriter
from reportlab.pdfgen import canvas
from reportlab.lib.pagesizes import letter
from io import BytesIO

# Create watermark
packet = BytesIO()
c = canvas.Canvas(packet, pagesize=letter)
c.setFont('Helvetica', 60)
c.setFillColorRGB(0.5, 0.5, 0.5, 0.3)
c.rotate(45)
c.drawString(200, 100, 'WATERMARK')
c.save()
packet.seek(0)

watermark = PdfReader(packet)
reader = PdfReader('input.pdf')
writer = PdfWriter()

for page in reader.pages:
    page.merge_page(watermark.pages[0])
    writer.add_page(page)

with open('watermarked.pdf', 'wb') as output:
    writer.write(output)
```

### Get PDF metadata

```python
from PyPDF2 import PdfReader

reader = PdfReader('input.pdf')
meta = reader.metadata

print(f'Title: {meta.title}')
print(f'Author: {meta.author}')
print(f'Pages: {len(reader.pages)}')
```

### Rotate pages

```python
from PyPDF2 import PdfReader, PdfWriter

reader = PdfReader('input.pdf')
writer = PdfWriter()

for page in reader.pages:
    page.rotate(90)  # Rotate 90 degrees clockwise
    writer.add_page(page)

with open('rotated.pdf', 'wb') as output:
    writer.write(output)
```

### Encrypt PDF

```python
from PyPDF2 import PdfReader, PdfWriter

reader = PdfReader('input.pdf')
writer = PdfWriter()

for page in reader.pages:
    writer.add_page(page)

writer.encrypt('user_password', 'owner_password')

with open('encrypted.pdf', 'wb') as output:
    writer.write(output)
```

## Tips

- Use pdfplumber for accurate text extraction (better than PyPDF2)
- Use reportlab for creating complex PDFs with precise control
- PyPDF2 is best for merging, splitting, and basic manipulation
- For forms, consider pdfrw or PyMuPDF (fitz)
- OCR requires additional tools like pytesseract for scanned PDFs

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tankygranny05) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
