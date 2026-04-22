---
name: document-skillsxlsx
description: Comprehensive spreadsheet creation, editing, and analysis with support for formulas, formatting, data analysis, and visualization. Use when working with spreadsheets (.xlsx, .xlsm, .csv, .tsv) for creating, reading, analyzing, modifying, or recalculating formulas. Use when this capability is needed.
metadata:
  author: tankygranny05
---

# Excel/Spreadsheet Skills

## Overview

This skill provides comprehensive spreadsheet manipulation capabilities using Python's openpyxl library for .xlsx files and pandas for data analysis.

## When to Use

- Creating new spreadsheets with formulas and formatting
- Reading or analyzing spreadsheet data
- Modifying existing spreadsheets while preserving formulas
- Data analysis and visualization in spreadsheets
- Recalculating formulas
- Converting between CSV/TSV and Excel formats

## Dependencies

```bash
pip install openpyxl pandas xlrd xlwt
```

## Quick Reference

### Create a new workbook

```python
from openpyxl import Workbook
from openpyxl.styles import Font, PatternFill, Alignment, Border, Side

wb = Workbook()
ws = wb.active
ws.title = "Sheet1"

# Write data
ws['A1'] = "Header"
ws['A1'].font = Font(bold=True)

# Add formula
ws['B10'] = "=SUM(B1:B9)"

wb.save("output.xlsx")
```

### Read existing workbook

```python
from openpyxl import load_workbook

wb = load_workbook("input.xlsx", data_only=False)  # data_only=True for calculated values
ws = wb.active

# Read cell
value = ws['A1'].value

# Iterate rows
for row in ws.iter_rows(min_row=1, max_row=10, values_only=True):
    print(row)
```

### Data analysis with pandas

```python
import pandas as pd

# Read Excel
df = pd.read_excel("input.xlsx", sheet_name="Sheet1")

# Analyze
summary = df.describe()
pivot = df.pivot_table(values='Amount', index='Category', aggfunc='sum')

# Write back
df.to_excel("output.xlsx", index=False)
```

### Formatting

```python
from openpyxl.styles import Font, PatternFill, Alignment

# Bold header
ws['A1'].font = Font(bold=True, size=14, color="FFFFFF")

# Background color
ws['A1'].fill = PatternFill(start_color="4472C4", end_color="4472C4", fill_type="solid")

# Alignment
ws['A1'].alignment = Alignment(horizontal="center", vertical="center")

# Column width
ws.column_dimensions['A'].width = 20

# Number format
ws['B1'].number_format = '$#,##0.00'
ws['C1'].number_format = '0.00%'
```

### Common Formulas

```python
# SUM
ws['A10'] = '=SUM(A1:A9)'

# AVERAGE
ws['A11'] = '=AVERAGE(A1:A9)'

# VLOOKUP
ws['C1'] = '=VLOOKUP(A1,Sheet2!A:B,2,FALSE)'

# IF
ws['D1'] = '=IF(A1>100,"High","Low")'

# COUNTIF
ws['E1'] = '=COUNTIF(A:A,">0")'
```

## Tips

- Use `data_only=True` when loading to get calculated values instead of formulas
- Always save with `.xlsx` extension for full feature support
- Use pandas for heavy data manipulation, openpyxl for formatting
- Preserve formulas by not using `data_only=True` when modifying

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tankygranny05) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
