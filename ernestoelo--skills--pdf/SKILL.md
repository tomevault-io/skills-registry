---
name: pdf
description: This skill should be used when the user asks to "merge PDFs", "split a PDF", "extract text from PDF", "OCR a scanned PDF", "create a PDF", "add watermark", mentions "PDF processing", or discusses PDF manipulation, conversion, or automation. Use when this capability is needed.
metadata:
  author: ernestoelo
---

# PDF Skill

Advanced PDF operations: merging, splitting, extracting, OCR, creating, and format conversion.

## When to Use
- Extracting or analyzing content from PDFs
- Merging, splitting, or securing PDF files
- Converting PDFs to/from other formats (Excel, Word)
- Automating PDF workflows in data pipelines
- Adding watermarks, encryption, or metadata

## Usage Examples

### Merge PDFs
```python
from pypdf import PdfWriter, PdfReader

writer = PdfWriter()
for pdf_file in ["doc1.pdf", "doc2.pdf"]:
    reader = PdfReader(pdf_file)
    for page in reader.pages:
        writer.add_page(page)
with open("merged.pdf", "wb") as output:
    writer.write(output)
```

### Extract Text from Scanned PDFs (OCR)
```python
import pytesseract
from pdf2image import convert_from_path

images = convert_from_path('scanned.pdf')
text = "".join([pytesseract.image_to_string(img) for img in images])
```

### Command-Line
```bash
pdftotext input.pdf output.txt
qpdf --empty --pages file1.pdf file2.pdf -- merged.pdf
```

### Create PDFs from Scratch
```python
from reportlab.lib.pagesizes import letter
from reportlab.pdfgen import canvas

c = canvas.Canvas("hello_world.pdf", pagesize=letter)
c.drawString(100, 750, "Hello, World!")
c.save()
```

### Add Watermarks
```python
from pypdf import PdfReader, PdfWriter

watermark = PdfReader("watermark.pdf").pages[0]
reader = PdfReader("document.pdf")
writer = PdfWriter()

for page in reader.pages:
    page.merge_page(watermark)
    writer.add_page(page)

with open("watermarked.pdf", "wb") as output:
    writer.write(output)
```

## Best Practices
- Ensure Python libraries (`pypdf`, `pdfplumber`) and CLI tools (`pdftotext`, `qpdf`) are installed
- OCR (`pytesseract`) works best with high-resolution scanned documents
- Avoid Unicode subscripts/superscripts with ReportLab due to font limitations
- Always validate output files after processing

## References
- See scripts/ for bundled automation utilities

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ernestoelo) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
