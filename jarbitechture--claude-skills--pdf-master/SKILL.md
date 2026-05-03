---
name: pdf-master
description: > Use when this capability is needed.
metadata:
  author: jarbitechture
---

# PDF Master — Universal PDF Workbench

## Philosophy

The user speaks in plain English (or whatever language). They say what they want done to their PDF.
You figure out which tools, libraries, and scripts to use. Never ask the user to choose a library
or explain technical details unless they specifically want that.

## Instruction Interpretation

When the user gives an NLP instruction, decompose it into one or more atomic operations from the
**Operation Registry** below. Chain them in the correct order. If ambiguous, pick the most likely
interpretation and state your assumption briefly.

### Instruction Examples → Operations

| User says | Operations |
|-----------|-----------|
| "Merge these three PDFs" | MERGE |
| "Pull out pages 5-10" | SPLIT/EXTRACT_PAGES |
| "Get me all the tables as a spreadsheet" | EXTRACT_TABLES → EXPORT_XLSX |
| "Rotate the second page landscape" | ROTATE(page=2, degrees=90) |
| "Stamp 'DRAFT' across every page" | WATERMARK(text="DRAFT") |
| "Lock it with a password" | ENCRYPT |
| "Remove the password" | DECRYPT |
| "Compress this, it's too big" | COMPRESS |
| "Add page numbers at the bottom" | ADD_PAGE_NUMBERS |
| "Make it searchable" (scanned doc) | OCR |
| "Fill in the form with my info" | FILL_FORM |
| "Delete page 3" | DELETE_PAGES |
| "Crop off the margins" | CROP |
| "What's in this PDF?" | READ/ANALYZE |
| "Convert to Word" | EXPORT_DOCX |
| "Redact all SSNs" | REDACT(pattern=SSN) |
| "Add a cover page" | CREATE_PAGE + PREPEND |
| "Extract all images" | EXTRACT_IMAGES |
| "Fix this broken PDF" | REPAIR |
| "Compare these two PDFs" | DIFF |
| "Flatten the form fields" | FLATTEN |
| "Add a header with the date" | ADD_HEADER |
| "Reorder: put page 5 first" | REORDER_PAGES |
| "Change 'Draft' to 'Final' everywhere" | EDIT_TEXT(find="Draft", replace="Final") |
| "Replace the date on page 1" | EDIT_TEXT(page=1) |
| "Add a note at the top of page 2" | ADD_TEXT(page=2) |
| "Put 'Approved' next to the signature line" | ADD_TEXT |
| "Black out all phone numbers" | REDACT(pattern=phone) |
| "Remove all email addresses" | REDACT(pattern=email) |
| "Make a PDF from this text/data" | CREATE |

## Operation Registry

Scripts live in `scripts/`. All CLI commands use 1-based page numbers.

### Operations with CLI commands (use `pdf_ops.py`)

```
python pdf_ops.py <command> [args...]

READ/ANALYZE:   python pdf_analyze.py input.pdf [--json]
MERGE:          python pdf_ops.py merge output.pdf file1.pdf file2.pdf ...
SPLIT:          python pdf_ops.py split input.pdf output_dir/ --pages 1,3,5-8
DELETE:         python pdf_ops.py delete input.pdf output.pdf --pages 2,4
REORDER:        python pdf_ops.py reorder input.pdf output.pdf --order 3,1,2,4
ROTATE:         python pdf_ops.py rotate input.pdf output.pdf --pages 1,3 --degrees 90
EXTRACT_TEXT:   python pdf_ops.py extract_text input.pdf [--output text.txt]
EXTRACT_TABLES: python pdf_ops.py extract_tables input.pdf [--output tables.xlsx]
EXTRACT_IMAGES: python pdf_ops.py extract_images input.pdf output_dir/
WATERMARK:      python pdf_ops.py watermark input.pdf output.pdf --text "DRAFT" [--opacity 0.15] [--fontsize 60]
PAGE_NUMBERS:   python pdf_ops.py add_page_numbers input.pdf output.pdf [--format "Page {n} of {total}"]
ENCRYPT:        python pdf_ops.py encrypt input.pdf output.pdf --password SECRET
DECRYPT:        python pdf_ops.py decrypt input.pdf output.pdf --password SECRET
COMPRESS:       python pdf_ops.py compress input.pdf output.pdf
RENDER:         python pdf_ops.py render input.pdf output_dir/ [--dpi 300]
EDIT_TEXT:      python pdf_ops.py edit_text input.pdf output.pdf --find "old" --replace "new" [--pages 1,3]
ADD_TEXT:       python pdf_ops.py add_text input.pdf output.pdf --text "Hello" --x 100 --y 700 [--pages 1] [--fontsize 12] [--font Helvetica]
REDACT:         python pdf_ops.py redact input.pdf output.pdf --find "regex_pattern" [--pages 1,3]
```

**EDIT_TEXT notes**: works reliably on simple/single-column PDFs. Complex layouts may misalign — verify visually.
**REDACT warning**: cosmetic only (text stays in PDF stream). Chain REDACT → FLATTEN for true redaction.
**ADD_TEXT defaults**: top of page (y=750), left margin (x=72) when user doesn't specify position.
**Common redact patterns**: SSNs → `\d{3}-\d{2}-\d{4}`, emails → `[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}`, phones → `\(?\d{3}\)?[-.\s]?\d{3}[-.\s]?\d{4}`

### Operations without CLI (use inline Python)

### CROP
```python
from pypdf import PdfReader, PdfWriter
reader = PdfReader(input_path)
writer = PdfWriter()
for page in reader.pages:
    page.mediabox.left = left_pts
    page.mediabox.bottom = bottom_pts
    page.mediabox.right = right_pts
    page.mediabox.top = top_pts
    writer.add_page(page)
with open(output_path, "wb") as f:
    writer.write(f)
```

### ADD_HEADER / ADD_FOOTER
Same pattern as ADD_TEXT — create a reportlab overlay positioned at top (header) or bottom (footer), merge onto each page.

### OCR (Scanned PDFs)
```python
import pytesseract
from pdf2image import convert_from_path
images = convert_from_path(input_path, dpi=300)
text = ""
for i, img in enumerate(images):
    text += f"--- Page {i+1} ---\n"
    text += pytesseract.image_to_string(img) + "\n\n"
```

### CREATE (from scratch)
```python
from reportlab.platypus import SimpleDocTemplate, Paragraph, Spacer, PageBreak
from reportlab.lib.styles import getSampleStyleSheet
from reportlab.lib.pagesizes import letter
doc = SimpleDocTemplate(output_path, pagesize=letter)
styles = getSampleStyleSheet()
story = []
doc.build(story)
```

### FILL_FORM
```python
from pypdf import PdfReader, PdfWriter
reader = PdfReader(input_path)
writer = PdfWriter()
writer.append(reader)
fields = reader.get_fields()
if fields:
    writer.update_page_form_field_values(writer.pages[0], {"field_name": "value"})
with open(output_path, "wb") as f:
    writer.write(f)
```
For non-fillable PDFs, use ADD_TEXT to place text at specific coordinates.

### FLATTEN
```python
from pypdf import PdfReader, PdfWriter
reader = PdfReader(input_path)
writer = PdfWriter()
writer.append(reader)
writer.flatten_form()
with open(output_path, "wb") as f:
    writer.write(f)
```

### REPAIR
```bash
qpdf --replace-input damaged.pdf
```

## Chaining Operations

When the user's instruction implies multiple operations, chain them. Use intermediate
files as intermediates and output the final result alongside the input file or where the user specifies.

**Example chain**: "Merge these PDFs, add page numbers, stamp CONFIDENTIAL, and password-protect it"
→ MERGE → ADD_PAGE_NUMBERS → WATERMARK → ENCRYPT → output

## Error Handling

1. **Encrypted PDF**: Try decrypting with empty string first. If that fails, ask for password.
2. **Scanned PDF**: Detect via empty text extraction. Suggest OCR if user wants text.
3. **Corrupted PDF**: Try `qpdf --replace-input` first. Report if unrecoverable.
4. **Large PDF**: Process in chunks of 10-50 pages. Warn if > 200 pages.
5. **Missing fonts**: Use pypdfium2 for rendering instead of reportlab for reading.

## Output Protocol

1. Save output alongside the input file, or where the user specifies
2. For multi-file outputs (split PDFs, extracted images), zip them first
3. State what was done in 1-2 sentences. No bloat.

## Quick Reference: Tool Selection

| Need | Best tool |
|------|-----------|
| Read/write pages, merge, split | pypdf |
| Extract text with layout | pdfplumber or `pdftotext -layout` |
| Extract tables | pdfplumber |
| Create PDFs from scratch | reportlab |
| Render pages to images | pypdfium2 or `pdftoppm` |
| Extract embedded images | `pdfimages -j` |
| Compress/optimize | `qpdf --optimize-images` |
| Repair | `qpdf --replace-input` |
| Encrypt/decrypt (CLI) | `qpdf` |
| OCR scanned docs | pytesseract + pdf2image |
| Find/replace text | `pdf_ops.py edit_text` |
| Insert text at position | `pdf_ops.py add_text` |
| Redact by pattern | `pdf_ops.py redact` (cosmetic — chain with FLATTEN) |
| Fill forms | See FILL_FORM section above |

## Important Notes

- **NEVER use unicode subscript/superscript characters** in reportlab — they render as black boxes. Use `<sub>` and `<super>` tags in Paragraph objects.
- **Always verify output** by rendering the first page to an image and examining it when doing visual operations (watermarks, stamps, form fills, page numbers).
- **Coordinate systems differ**: pdfplumber uses top-left origin (y increases downward). PDF native uses bottom-left (y increases upward). reportlab uses bottom-left. Always convert correctly.
- When the user uploads a PDF, first run ANALYZE to understand what you're working with before modifying.

---
> Source: [jarbitechture/claude-skills](https://github.com/jarbitechture/claude-skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-04-26 -->
