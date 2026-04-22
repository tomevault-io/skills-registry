---
name: document-skillsdocx
description: Comprehensive document creation, editing, and analysis with support for tracked changes, comments, formatting preservation, and text extraction. Use when working with Word documents (.docx) for creating, modifying, editing content, working with tracked changes, or adding comments. Use when this capability is needed.
metadata:
  author: tankygranny05
---

# Word Document Skills

## Overview

This skill provides comprehensive Word document manipulation capabilities using Python's python-docx library.

## When to Use

- Creating new Word documents
- Modifying or editing existing document content
- Working with tracked changes
- Adding comments
- Extracting text from documents
- Formatting documents (fonts, styles, headers, footers)

## Dependencies

```bash
pip install python-docx
```

## Quick Reference

### Create a new document

```python
from docx import Document
from docx.shared import Inches, Pt
from docx.enum.text import WD_ALIGN_PARAGRAPH

doc = Document()

# Add title
title = doc.add_heading('Document Title', level=0)

# Add paragraph
para = doc.add_paragraph('This is a paragraph.')

# Add formatted text
para = doc.add_paragraph()
run = para.add_run('Bold text')
run.bold = True
run = para.add_run(' and ')
run = para.add_run('italic text')
run.italic = True

doc.save('output.docx')
```

### Read existing document

```python
from docx import Document

doc = Document('input.docx')

# Read all paragraphs
for para in doc.paragraphs:
    print(para.text)

# Read tables
for table in doc.tables:
    for row in table.rows:
        for cell in row.cells:
            print(cell.text)
```

### Add tables

```python
from docx import Document
from docx.shared import Inches

doc = Document()

# Create table
table = doc.add_table(rows=3, cols=3)
table.style = 'Table Grid'

# Fill cells
for i, row in enumerate(table.rows):
    for j, cell in enumerate(row.cells):
        cell.text = f'Row {i+1}, Col {j+1}'

# Set column widths
for cell in table.columns[0].cells:
    cell.width = Inches(2)

doc.save('output.docx')
```

### Formatting

```python
from docx import Document
from docx.shared import Pt, RGBColor
from docx.enum.text import WD_ALIGN_PARAGRAPH

doc = Document()

# Paragraph alignment
para = doc.add_paragraph('Centered text')
para.alignment = WD_ALIGN_PARAGRAPH.CENTER

# Font formatting
para = doc.add_paragraph()
run = para.add_run('Formatted text')
run.font.name = 'Arial'
run.font.size = Pt(14)
run.font.color.rgb = RGBColor(0x42, 0x72, 0xC4)
run.font.bold = True

# Line spacing
para_format = para.paragraph_format
para_format.line_spacing = 1.5

doc.save('output.docx')
```

### Headers and Footers

```python
from docx import Document

doc = Document()

# Add header
section = doc.sections[0]
header = section.header
header_para = header.paragraphs[0]
header_para.text = "Document Header"

# Add footer
footer = section.footer
footer_para = footer.paragraphs[0]
footer_para.text = "Page Footer"

doc.save('output.docx')
```

### Add images

```python
from docx import Document
from docx.shared import Inches

doc = Document()
doc.add_picture('image.png', width=Inches(4))
doc.save('output.docx')
```

### Styles

```python
from docx import Document

doc = Document()

# Use built-in styles
doc.add_heading('Heading 1', level=1)
doc.add_heading('Heading 2', level=2)
doc.add_paragraph('Normal paragraph')
doc.add_paragraph('Quote style', style='Quote')
doc.add_paragraph('List item', style='List Bullet')

doc.save('output.docx')
```

## Tips

- Use styles for consistent formatting
- Access runs within paragraphs for fine-grained text control
- Use sections to control page layout, margins, orientation
- Tables can be nested for complex layouts

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tankygranny05) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
