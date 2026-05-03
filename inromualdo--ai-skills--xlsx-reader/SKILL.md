---
name: xlsx-reader
description: Read and extract data from Excel (.xlsx) files efficiently using Python. Use when opening, reading, analyzing, or inspecting xlsx/Excel spreadsheets, extracting tabular data to JSON, inspecting sheet structure or headers, converting spreadsheet data for processing, or any task involving .xlsx files. Triggers on references to xlsx, Excel, spreadsheet files, or data extraction from workbooks. Use when this capability is needed.
metadata:
  author: inromualdo
---

Read xlsx files with `scripts/read_xlsx.py`. Uses `uv run` for automatic dependency management.

## Quick Reference

```bash
# Get structure without data (always start here)
uv run ${SKILL_DIR}/scripts/read_xlsx.py file.xlsx --mode info

# Preview first 10 rows
uv run ${SKILL_DIR}/scripts/read_xlsx.py file.xlsx --rows 10

# Read specific sheet
uv run ${SKILL_DIR}/scripts/read_xlsx.py file.xlsx --sheet "Sheet2"

# Read cell range
uv run ${SKILL_DIR}/scripts/read_xlsx.py file.xlsx --mode range --range B2:D50

# Full data (avoid for large files)
uv run ${SKILL_DIR}/scripts/read_xlsx.py file.xlsx --mode full

# Force/disable header detection
uv run ${SKILL_DIR}/scripts/read_xlsx.py file.xlsx --headers
uv run ${SKILL_DIR}/scripts/read_xlsx.py file.xlsx --no-headers
```

## Modes

| Mode | Use Case | Output Size |
|------|----------|-------------|
| `info` | Structure, sheets, dimensions, headers | Minimal |
| `preview` | First N rows (default 20) | Small |
| `range` | Specific cells (e.g., A1:C10) | Targeted |
| `full` | All data | Large - use sparingly |

## Workflow

1. **Always start with `--mode info`** to understand structure
2. Use `--mode preview` with appropriate `--rows` for context
3. Use `--mode range` for targeted extraction
4. Avoid `--mode full` on large files (>1000 rows)

## Output Format

All modes return JSON. Preview/full modes with detected headers return data as objects:

```json
{
  "mode": "preview",
  "sheet": "Sheet1",
  "headers": ["Name", "Value", "Status"],
  "data": [
    {"Name": "item_1", "Value": 10, "Status": "active"},
    {"Name": "item_2", "Value": 20, "Status": "inactive"}
  ],
  "total_rows": 500,
  "showing": 2,
  "has_more": true
}
```

Without headers (or `--no-headers`), `data` is arrays of arrays. Duplicate header names get `_2`, `_3` suffixes automatically.

## Error Handling

Errors return `{"error": "message"}` with exit code 1. Common cases:
- Sheet not found → error includes available sheet names
- Missing `--range` for range mode → clear error message
- File not found → file path in error

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/inromualdo) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
