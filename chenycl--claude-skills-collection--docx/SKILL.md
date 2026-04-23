---
name: docx
description: > Use when this capability is needed.
metadata:
  author: chenycl
---

# Word Document Handler

Create, read, and edit Microsoft Word (.docx) documents with python-docx.

## Capabilities

- Create professional documents from scratch
- Read and extract text from .docx files
- Format with headings, styles, fonts, colors
- Add tables, images, and lists
- Create tables of contents
- Work with headers, footers, page numbers
- Handle tracked changes and comments
- Use templates and document sections

## Python Library

```python
from docx import Document
from docx.shared import Inches, Pt, RGBColor
from docx.enum.text import WD_ALIGN_PARAGRAPH
from docx.enum.style import WD_STYLE_TYPE
from docx.enum.table import WD_TABLE_ALIGNMENT
```

## Common Operations

### Create New Document
```python
from docx import Document
from docx.shared import Pt

doc = Document()

# Add title
doc.add_heading("Document Title", level=0)

# Add paragraph
doc.add_paragraph("This is the first paragraph of the document.")

# Add formatted paragraph
p = doc.add_paragraph()
run = p.add_run("Bold text")
run.bold = True
p.add_run(" and ")
run = p.add_run("italic text")
run.italic = True

doc.save("document.docx")
```

### Add Headings
```python
doc.add_heading("Main Section", level=1)
doc.add_heading("Subsection", level=2)
doc.add_heading("Sub-subsection", level=3)
```

### Format Text
```python
from docx.shared import Pt, RGBColor

p = doc.add_paragraph()
run = p.add_run("Formatted text")
run.font.name = "Arial"
run.font.size = Pt(14)
run.font.bold = True
run.font.color.rgb = RGBColor(0x00, 0x66, 0xCC)
```

### Add Bullet List
```python
doc.add_paragraph("First item", style="List Bullet")
doc.add_paragraph("Second item", style="List Bullet")
doc.add_paragraph("Third item", style="List Bullet")
```

### Add Numbered List
```python
doc.add_paragraph("Step one", style="List Number")
doc.add_paragraph("Step two", style="List Number")
doc.add_paragraph("Step three", style="List Number")
```

### Add Table
```python
table = doc.add_table(rows=3, cols=3)
table.style = "Table Grid"

# Header row
hdr_cells = table.rows[0].cells
hdr_cells[0].text = "Name"
hdr_cells[1].text = "Age"
hdr_cells[2].text = "City"

# Data rows
row = table.rows[1].cells
row[0].text = "Alice"
row[1].text = "30"
row[2].text = "New York"
```

### Add Image
```python
from docx.shared import Inches

doc.add_picture("image.png", width=Inches(4))
```

### Add Header and Footer
```python
section = doc.sections[0]

# Header
header = section.header
header_para = header.paragraphs[0]
header_para.text = "Document Header"

# Footer
footer = section.footer
footer_para = footer.paragraphs[0]
footer_para.text = "Page "
```

### Add Page Break
```python
doc.add_page_break()
```

### Read Existing Document
```python
doc = Document("existing.docx")

for para in doc.paragraphs:
    print(para.text)

for table in doc.tables:
    for row in table.rows:
        for cell in row.cells:
            print(cell.text)
```

### Find and Replace
```python
def find_replace(doc, find_text, replace_text):
    for para in doc.paragraphs:
        if find_text in para.text:
            for run in para.runs:
                if find_text in run.text:
                    run.text = run.text.replace(find_text, replace_text)
    return doc
```

### Set Margins
```python
from docx.shared import Inches

section = doc.sections[0]
section.top_margin = Inches(1)
section.bottom_margin = Inches(1)
section.left_margin = Inches(1.25)
section.right_margin = Inches(1.25)
```

### Add Table of Contents
```python
# Note: TOC needs Word to update field codes
from docx.oxml.ns import qn
from docx.oxml import OxmlElement

paragraph = doc.add_paragraph()
run = paragraph.add_run()
fldChar = OxmlElement('w:fldChar')
fldChar.set(qn('w:fldCharType'), 'begin')
run._r.append(fldChar)

run = paragraph.add_run()
instrText = OxmlElement('w:instrText')
instrText.text = 'TOC \\o "1-3" \\h \\z \\u'
run._r.append(instrText)

run = paragraph.add_run()
fldChar = OxmlElement('w:fldChar')
fldChar.set(qn('w:fldCharType'), 'end')
run._r.append(fldChar)
```

## Document Styles

| Style Name | Use Case |
|------------|----------|
| Title | Document title |
| Heading 1-9 | Section headings |
| Normal | Body text |
| List Bullet | Bullet points |
| List Number | Numbered lists |
| Quote | Block quotes |
| Intense Quote | Emphasized quotes |

## Best Practices

1. **Use styles** - Maintain consistency with built-in styles
2. **Structure** - Use headings for document outline
3. **Tables** - Use "Table Grid" style for visibility
4. **Images** - Set appropriate width, maintain aspect ratio
5. **Fonts** - Stick to common fonts (Arial, Times, Calibri)
6. **Spacing** - Use paragraph spacing, not empty paragraphs

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/chenycl) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
