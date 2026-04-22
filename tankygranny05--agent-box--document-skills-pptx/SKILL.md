---
name: document-skillspptx
description: Presentation creation, editing, and analysis. Use when working with PowerPoint presentations (.pptx) for creating new presentations, modifying or editing content, working with layouts, or adding comments or speaker notes. Use when this capability is needed.
metadata:
  author: tankygranny05
---

# PowerPoint Presentation Skills

## Overview

This skill provides comprehensive PowerPoint manipulation capabilities using Python's python-pptx library.

## When to Use

- Creating new presentations
- Modifying or editing existing slides
- Working with slide layouts and masters
- Adding speaker notes
- Inserting images, charts, and tables
- Extracting content from presentations

## Dependencies

```bash
pip install python-pptx
```

## Quick Reference

### Create a new presentation

```python
from pptx import Presentation
from pptx.util import Inches, Pt

prs = Presentation()

# Add title slide
title_slide_layout = prs.slide_layouts[0]
slide = prs.slides.add_slide(title_slide_layout)
title = slide.shapes.title
subtitle = slide.placeholders[1]

title.text = "Presentation Title"
subtitle.text = "Subtitle here"

prs.save('output.pptx')
```

### Add content slide

```python
from pptx import Presentation
from pptx.util import Inches, Pt

prs = Presentation()

# Title and Content layout (index 1)
bullet_slide_layout = prs.slide_layouts[1]
slide = prs.slides.add_slide(bullet_slide_layout)

title = slide.shapes.title
title.text = "Slide Title"

body = slide.placeholders[1]
tf = body.text_frame
tf.text = "First bullet point"

p = tf.add_paragraph()
p.text = "Second bullet point"
p.level = 0

p = tf.add_paragraph()
p.text = "Sub-bullet"
p.level = 1

prs.save('output.pptx')
```

### Read existing presentation

```python
from pptx import Presentation

prs = Presentation('input.pptx')

for slide in prs.slides:
    for shape in slide.shapes:
        if hasattr(shape, "text"):
            print(shape.text)
```

### Add images

```python
from pptx import Presentation
from pptx.util import Inches

prs = Presentation()
blank_slide_layout = prs.slide_layouts[6]
slide = prs.slides.add_slide(blank_slide_layout)

# Add image
left = Inches(1)
top = Inches(1)
width = Inches(5)
slide.shapes.add_picture('image.png', left, top, width=width)

prs.save('output.pptx')
```

### Add tables

```python
from pptx import Presentation
from pptx.util import Inches

prs = Presentation()
slide = prs.slides.add_slide(prs.slide_layouts[6])

# Create table
rows, cols = 3, 4
left = Inches(1)
top = Inches(2)
width = Inches(8)
height = Inches(2)

table = slide.shapes.add_table(rows, cols, left, top, width, height).table

# Fill cells
for i in range(rows):
    for j in range(cols):
        table.cell(i, j).text = f'R{i+1}C{j+1}'

# Style header row
for cell in table.rows[0].cells:
    cell.fill.solid()
    cell.fill.fore_color.rgb = RGBColor(0x42, 0x72, 0xC4)

prs.save('output.pptx')
```

### Add speaker notes

```python
from pptx import Presentation

prs = Presentation()
slide = prs.slides.add_slide(prs.slide_layouts[1])

# Add notes
notes_slide = slide.notes_slide
notes_tf = notes_slide.notes_text_frame
notes_tf.text = "These are the speaker notes for this slide."

prs.save('output.pptx')
```

### Text formatting

```python
from pptx import Presentation
from pptx.util import Pt
from pptx.dml.color import RGBColor

prs = Presentation()
slide = prs.slides.add_slide(prs.slide_layouts[6])

# Add text box
left = Inches(1)
top = Inches(1)
width = Inches(6)
height = Inches(1)

txBox = slide.shapes.add_textbox(left, top, width, height)
tf = txBox.text_frame
p = tf.paragraphs[0]
run = p.add_run()
run.text = "Formatted Text"

# Format
run.font.name = 'Arial'
run.font.size = Pt(24)
run.font.bold = True
run.font.color.rgb = RGBColor(0xFF, 0x00, 0x00)

prs.save('output.pptx')
```

### Slide layouts reference

```
Index 0: Title Slide
Index 1: Title and Content
Index 2: Section Header
Index 3: Two Content
Index 4: Comparison
Index 5: Title Only
Index 6: Blank
Index 7: Content with Caption
Index 8: Picture with Caption
```

## Tips

- Use slide layouts for consistent formatting
- Access shapes.title for the title placeholder
- Use placeholders[1] for the main content area
- EMU (English Metric Units) are used internally; use Inches() or Pt() helpers
- Charts require additional setup with pptx.chart

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tankygranny05) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
