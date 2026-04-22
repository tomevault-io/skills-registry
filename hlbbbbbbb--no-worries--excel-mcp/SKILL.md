---
name: excel-python
description: Excel 修改操作使用 Python + openpyxl Use when this capability is needed.
metadata:
  author: hlbbbbbbb
---

# Excel 完整功能 (Python + openpyxl)

## ⚠️ 核心规则

```
┌─────────────────────────────────────────────────────┐
│  所有 Excel 修改操作必须使用 Python！                  │
└─────────────────────────────────────────────────────┘
```

## 🎯 Python 处理的操作

| 操作类型 | 说明 |
|---------|------|
| **美化/格式化** | 边框、填充、字体、对齐 |
| **数据修改** | 写入、删除、修改单元格 |
| **样式设置** | 宋体、颜色、加粗等 |
| **数字格式** | 货币、百分比、日期 |
| **图表** | 柱状图、饼图等 |
| **公式** | 计算、求和等 |
| **结构操作** | 合并单元格、插入/删除行列 |

## ⚠️ 使用流程

```
1. 用户提供文件路径（或从对话中获取）
2. 复制原文件 → 新文件（命名如：原文件名_处理版.xlsx）
3. Python + openpyxl 在新文件上执行修改
4. 保存新文件并打印路径
```

**绝对不要直接修改原文件！** 先复制再改，原文件保持不变。

### 文件复制示例

```python
import shutil

# 第一次修改时：复制原文件
original_path = '/path/to/销售数据.xlsx'
new_path = '/path/to/销售数据_美化版.xlsx'
shutil.copy(original_path, new_path)

# 在新文件上修改
wb = openpyxl.load_workbook(new_path)
# ... 修改操作 ...
wb.save(new_path)
print(f"修改完成，保存在：{new_path}")
print(f"原文件保持不变：{original_path}")
```

**后续继续修改时**：直接在已有的副本上改，不需要再复制。

## 🎨 字体要求（CRITICAL）

**所有 Excel 文件必须使用宋体（SimSun）作为默认字体！**

- 表头字体：宋体
- 数据单元格：宋体
- 所有中文内容：宋体

## Quick Start

### Read Excel

```bash
python3 << 'EOF'
import openpyxl

wb = openpyxl.load_workbook('/path/to/file.xlsx', data_only=True)
print(f"Sheets: {wb.sheetnames}")

ws = wb.active
print(f"Headers: {[cell.value for cell in ws[1]]}")
print(f"Total rows: {ws.max_row}")

# Preview first 10 rows
for row in ws.iter_rows(max_row=10, values_only=True):
    print(row)
EOF
```

### Filter Data

```bash
python3 << 'EOF'
import openpyxl

wb = openpyxl.load_workbook('/path/to/file.xlsx', data_only=True)
ws = wb['Sheet1']

headers = [cell.value for cell in ws[1]]
col_idx = headers.index('Amount')  # Find column by name

results = []
for row in ws.iter_rows(min_row=2, values_only=True):
    if row[col_idx] and row[col_idx] > 1000:
        results.append(dict(zip(headers, row)))

print(f"Matched {len(results)} rows")
for r in results[:10]:
    print(r)
EOF
```

### Write Data

```bash
python3 << 'EOF'
import openpyxl
from openpyxl.styles import Font

wb = openpyxl.load_workbook('/path/to/file.xlsx')
ws = wb['Sheet1']

data = [
    ["Name", "Age", "City"],
    ["Alice", 30, "Beijing"],
    ["Bob", 25, "Shanghai"]
]

# 写入数据并设置宋体
for row_idx, row_data in enumerate(data, start=1):
    for col_idx, value in enumerate(row_data, start=1):
        cell = ws.cell(row=row_idx, column=col_idx, value=value)
        cell.font = Font(name='SimSun')  # 设置宋体

wb.save('/path/to/file.xlsx')
print("Done (宋体)")
EOF
```

### Format Cells

```bash
python3 << 'EOF'
import openpyxl
from openpyxl.styles import Font, Alignment, PatternFill

wb = openpyxl.load_workbook('/path/to/file.xlsx')
ws = wb['Sheet1']

# Column widths
ws.column_dimensions['A'].width = 30
ws.column_dimensions['B'].width = 50

# Row heights
for row_num in range(1, ws.max_row + 1):
    ws.row_dimensions[row_num].height = 25

# Header formatting (宋体 + 加粗)
for cell in ws[1]:
    cell.font = Font(name='SimSun', bold=True, size=12)  # 宋体
    cell.alignment = Alignment(horizontal='center')

# Data row formatting (宋体 + wrap text)
for row in ws.iter_rows(min_row=2):
    for cell in row:
        cell.font = Font(name='SimSun')  # 宋体
        cell.alignment = Alignment(wrap_text=True, vertical='top')

wb.save('/path/to/file.xlsx')
print("Formatting applied (宋体)")
EOF
```

## Common Operations Reference

| Operation | Code Pattern |
|-----------|--------------|
| Open file | `wb = openpyxl.load_workbook('file.xlsx')` |
| Open read-only | `wb = openpyxl.load_workbook('file.xlsx', data_only=True)` |
| Get sheet | `ws = wb['Sheet1']` or `ws = wb.active` |
| Read cell | `ws['A1'].value` or `ws.cell(row=1, column=1).value` |
| Write cell | `ws['A1'] = 'value'` or `ws.cell(row=1, column=1, value='value')` |
| Iterate rows | `for row in ws.iter_rows(min_row=2, values_only=True):` |
| Column width | `ws.column_dimensions['A'].width = 30` |
| Row height | `ws.row_dimensions[1].height = 25` |
| **设置宋体（必须）** | `cell.font = Font(name='SimSun')` |
| Bold font (宋体) | `cell.font = Font(name='SimSun', bold=True)` |
| Wrap text | `cell.alignment = Alignment(wrap_text=True)` |
| Save | `wb.save('file.xlsx')` |

## Runtime

openpyxl is pre-installed on most systems. If not:

```bash
pip3 install openpyxl
```

## Permission Workflow

1. 先复制原文件到新路径
2. `file-permission_request_file_permission({ "operation": "create", "filePath": "/path/to/新文件.xlsx" })`
3. Wait for "allowed"
4. Run Python script on the new file
5. 汇报：新文件位置 + 修改内容 + 原文件保持不变

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hlbbbbbbb) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
