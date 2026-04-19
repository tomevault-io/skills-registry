---
name: word-excel-automation
description: Best practices and patterns for manipulating Word (.docx) and Excel (.xlsx) documents using Python. Use when this capability is needed.
metadata:
  author: egooktafanda97
---

# Word & Excel Automation Skill

Skill untuk memanipulasi dokumen **Word (.docx)** dan **Excel (.xlsx)** menggunakan Python dengan library standar industri.

## Libraries

| Format | Library | Install |
|--------|---------|---------|
| Word | `python-docx` | `pip install python-docx` |
| Excel | `openpyxl` | `pip install openpyxl` |

---

## Word (python-docx) Patterns

### 1. Open & Save Document
```python
from docx import Document

doc = Document("template.docx")
# ... manipulate ...
doc.save("output.docx")
```

### 2. Access Tables
```python
table = doc.tables[0]  # First table
row = table.rows[0]
cell = row.cells[0]
```

### 3. Clear Cell Content
```python
while len(cell.paragraphs) > 0:
    p = cell.paragraphs[0]._element
    p.getparent().remove(p)
```

### 4. Add Formatted Paragraphs
```python
from docx.shared import Pt, Inches

p = cell.add_paragraph()
p.paragraph_format.left_indent = Inches(0.2)  # Indentation
p.paragraph_format.space_after = Pt(3)        # Spacing

run = p.add_run("Bold Text")
run.bold = True
run.font.size = Pt(10)
```

### 5. Duplicate Table Rows
```python
import copy

def duplicate_row(table, source_idx, insert_before_idx):
    row_to_clone = table.rows[source_idx]
    insert_row = table.rows[insert_before_idx]
    new_tr = copy.deepcopy(row_to_clone._tr)
    insert_row._tr.addprevious(new_tr)
    return table.rows[insert_before_idx]
```

### 6. List Formatting in Cells
```python
# Numbered List
if line[0].isdigit() and '. ' in line[:4]:
    p.paragraph_format.left_indent = Inches(0.2)
    p.text = line

# Bullet List
if line.startswith('•'):
    p.paragraph_format.left_indent = Inches(0.2)
    p.text = line

# Sub-bullet (indented)
if leading_spaces >= 2:
    p.paragraph_format.left_indent = Inches(0.4)
```

---

## Excel (openpyxl) Patterns

### 1. Open & Save Workbook
```python
from openpyxl import load_workbook

wb = load_workbook("template.xlsx")
ws = wb.active
# ... manipulate ...
wb.save("output.xlsx")
```

### 2. Read/Write Cells
```python
ws['A1'] = "Header"
ws.cell(row=2, column=1, value="Data")

# Read
value = ws['A1'].value
```

### 3. Iterate Rows
```python
for row in ws.iter_rows(min_row=2, max_col=5):
    for cell in row:
        print(cell.value)
```

### 4. Style Cells
```python
from openpyxl.styles import Font, Alignment, Border, Side

cell = ws['A1']
cell.font = Font(bold=True, size=12)
cell.alignment = Alignment(horizontal='center', vertical='center')
cell.border = Border(
    left=Side(style='thin'),
    right=Side(style='thin'),
    top=Side(style='thin'),
    bottom=Side(style='thin')
)
```

### 5. Merge Cells
```python
ws.merge_cells('A1:D1')
ws.unmerge_cells('A1:D1')
```

### 6. Column/Row Dimensions
```python
ws.column_dimensions['A'].width = 20
ws.row_dimensions[1].height = 30
```

---

## Best Practices

1. **Always use templates**: Start from a styled template, don't build from scratch
2. **Preserve formatting**: Copy styles from existing elements when possible
3. **Clear before write**: Remove existing content before adding new to avoid artifacts
4. **Use shared units**: `Pt` for font, `Inches` for margins/indents
5. **Test with inspection**: Create helper scripts to verify document structure

## Related Skills
- [academic_course_design](../academic_course_design/SKILL.md) - Uses Word automation for RP generation

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/egooktafanda97) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
