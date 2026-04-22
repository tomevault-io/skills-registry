---
name: word-python
description: Word automation using Python + python-docx. Generate, read, and modify Word documents (.docx) with proper formatting. Use when this capability is needed.
metadata:
  author: hlbbbbbbb
---

# Word Automation (Python + python-docx)

**IMPORTANT**: This skill uses direct Python scripts instead of MCP tools for better reliability, similar to excel-python.

## 🎨 字体要求（CRITICAL）

**所有 Word 文档必须使用宋体（SimSun）作为默认字体！**

- 标题字体：宋体
- 正文字体：宋体
- 表格字体：宋体
- 所有中文内容：宋体

在创建任何文档时，必须为所有文本元素设置字体为宋体！

## Why Python over MCP?

| Aspect | MCP Tools | Python Scripts |
|--------|-----------|----------------|
| Reliability | JSON parsing errors common | Stable, predictable |
| Speed | MCP server startup overhead | Direct execution |
| Flexibility | Limited to predefined tools | Full control |
| Debugging | Multi-layer errors | Clear error messages |
| Model-friendly | Complex nested JSON | Natural code |

## Quick Start

### Create Word Document

```bash
python3 << 'EOF'
from docx import Document
from docx.shared import Pt

doc = Document()

# Add heading (使用宋体)
heading = doc.add_heading('月度销售报告', 0)
for run in heading.runs:
    run.font.name = 'SimSun'  # 宋体
    run._element.rPr.rFonts.set('{http://schemas.openxmlformats.org/wordprocessingml/2006/main}eastAsia', 'SimSun')

# Add paragraph (使用宋体)
para = doc.add_paragraph('这是自动生成的报告内容。')
for run in para.runs:
    run.font.name = 'SimSun'  # 宋体
    run._element.rPr.rFonts.set('{http://schemas.openxmlformats.org/wordprocessingml/2006/main}eastAsia', 'SimSun')

# Save
doc.save('/path/to/report.docx')
print("✅ Word 文档已生成（宋体）")
EOF
```

### Create Report with Tables

```bash
python3 << 'EOF'
from docx import Document
from docx.shared import Pt
from docx.enum.text import WD_ALIGN_PARAGRAPH

def set_font_simsun(cell):
    """设置单元格字体为宋体"""
    for paragraph in cell.paragraphs:
        for run in paragraph.runs:
            run.font.name = 'SimSun'
            run._element.rPr.rFonts.set('{http://schemas.openxmlformats.org/wordprocessingml/2006/main}eastAsia', 'SimSun')

doc = Document()

# 标题（宋体）
heading = doc.add_heading('销售数据', 1)
for run in heading.runs:
    run.font.name = 'SimSun'
    run._element.rPr.rFonts.set('{http://schemas.openxmlformats.org/wordprocessingml/2006/main}eastAsia', 'SimSun')

# Add table
table = doc.add_table(rows=4, cols=3)
table.style = 'Light Grid Accent 1'

# Header
header_cells = table.rows[0].cells
header_cells[0].text = '产品'
header_cells[1].text = '销量'
header_cells[2].text = '金额'

# 设置表头字体为宋体
for cell in header_cells:
    set_font_simsun(cell)

# Data rows
data = [
    ['产品A', '100', '50000'],
    ['产品B', '200', '80000'],
    ['合计', '300', '130000']
]

for i, row_data in enumerate(data, 1):
    row_cells = table.rows[i].cells
    for j, cell_data in enumerate(row_data):
        row_cells[j].text = cell_data
        set_font_simsun(row_cells[j])  # 设置宋体

doc.save('/path/to/report.docx')
print("✅ 带表格的报告已生成（宋体）")
EOF
```

### Read Word Document

```bash
python3 << 'EOF'
from docx import Document

doc = Document('/path/to/file.docx')

# Read all paragraphs
for para in doc.paragraphs:
    print(para.text)

# Read all tables
for table in doc.tables:
    print("\n表格:")
    for row in table.rows:
        print([cell.text for cell in row.cells])
EOF
```

### Add Formatted Text

```bash
python3 << 'EOF'
from docx import Document
from docx.shared import Pt, RGBColor
from docx.enum.text import WD_ALIGN_PARAGRAPH

doc = Document()

# Bold heading
para = doc.add_paragraph('重要提示')
run = para.runs[0]
run.bold = True
run.font.size = Pt(14)
run.font.color.rgb = RGBColor(255, 0, 0)

# Center paragraph
para.alignment = WD_ALIGN_PARAGRAPH.CENTER

doc.save('/path/to/formatted.docx')
print("✅ 格式化文档已生成")
EOF
```

### Convert Markdown to Word (Clean Formatting)

```bash
python3 << 'EOF'
from docx import Document
from docx.shared import Pt
import re

def markdown_to_word(md_content):
    doc = Document()

    for line in md_content.split('\n'):
        if line.startswith('### '):
            doc.add_heading(line[4:], 2)
        elif line.startswith('## '):
            doc.add_heading(line[3:], 1)
        elif line.startswith('# '):
            doc.add_heading(line[2:], 0)
        elif line.strip() == '':
            continue
        else:
            # Remove markdown markers
            text = re.sub(r'\*\*(.*?)\*\*', r'\1', line)
            text = re.sub(r'\*(.*?)\*', r'\1', text)
            text = re.sub(r'`([^`]+)`', r'\1', text)
            doc.add_paragraph(text)

    return doc

md_text = """
# 报告标题

## 数据分析

这是**重要**数据。

## 结论
关键结论。
"""

doc = markdown_to_word(md_text)
doc.save('/path/to/converted.docx')
print("✅ Markdown 转 Word 完成")
EOF
```

### Create Document with Complex Table

```bash
python3 << 'EOF'
from docx import Document
from docx.shared import Pt, RGBColor
from docx.enum.text import WD_ALIGN_PARAGRAPH
from docx.styles import ParagraphStyle

doc = Document()

# Title
doc.add_heading('项目进度报告', 0)

# Add table
table = doc.add_table(rows=5, cols=4)
table.style = 'Light Grid Accent 1'

# Header cells
headers = table.rows[0].cells
headers[0].text = '任务'
headers[1].text = '负责人'
headers[2].text = '状态'
headers[3].text = '截止日期'

# Format header
for cell in headers:
    run = cell.paragraphs[0].runs[0]
    run.bold = True

# Data
data = [
    ['需求分析', '张三', '已完成', '2024-01-15'],
    ['UI设计', '李四', '进行中', '2024-01-20'],
    ['前端开发', '王五', '进行中', '2024-01-25'],
    ['测试', '赵六', '未开始', '2024-01-30']
]

for i, row_data in enumerate(data, 1):
    cells = table.rows[i].cells
    for j, cell_data in enumerate(row_data):
        cells[j].text = cell_data

doc.save('/path/to/progress.docx')
print("✅ 复杂表格文档已生成")
EOF
```

## Common Operations Reference

| Operation | Code Pattern |
|-----------|--------------|
| Create document | `doc = Document()` |
| Add heading | `doc.add_heading('Title', 0)` (0=highest) |
| Add paragraph | `doc.add_paragraph('Text')` |
| Add table | `doc.add_table(rows=3, cols=2)` |
| Set table style | `table.style = 'Light Grid Accent 1'` |
| Access cell | `table.rows[0].cells[0].text = 'Value'` |
| **设置宋体（必须）** | `run.font.name = 'SimSun'` + `run._element.rPr.rFonts.set('{http://schemas.openxmlformats.org/wordprocessingml/2006/main}eastAsia', 'SimSun')` |
| Bold text | `run.bold = True` |
| Font size | `run.font.size = Pt(14)` |
| Font color | `run.font.color.rgb = RGBColor(255, 0, 0)` |
| Center align | `para.alignment = WD_ALIGN_PARAGRAPH.CENTER` |
| Save | `doc.save('file.docx')` |

### 🎨 宋体设置函数（推荐复用）

```python
def set_simsun_font(run):
    """为 run 设置宋体字体"""
    run.font.name = 'SimSun'
    run._element.rPr.rFonts.set('{http://schemas.openxmlformats.org/wordprocessingml/2006/main}eastAsia', 'SimSun')

def set_paragraph_simsun(paragraph):
    """为整个段落设置宋体"""
    for run in paragraph.runs:
        set_simsun_font(run)

def set_cell_simsun(cell):
    """为表格单元格设置宋体"""
    for paragraph in cell.paragraphs:
        set_paragraph_simsun(paragraph)
```

## Runtime

python-docx is pre-installed on most systems. If not:

```bash
pip3 install python-docx
```

## Permission Workflow

ALWAYS request permission before writing:

1. `file-permission_request_file_permission({ "operation": "create", "filePath": "/path/to/report.docx" })`
2. Wait for "allowed"
3. Run Python script

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hlbbbbbbb) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
