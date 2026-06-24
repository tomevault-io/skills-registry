---
name: pdf
description: | Use when this capability is needed.
metadata:
  author: holsee
---

# PDF Skill

This skill provides comprehensive PDF manipulation capabilities.

## Available Operations

### Text Extraction

Use `pdftotext` or Python's `pdfplumber` to extract text:

```bash
pdftotext input.pdf output.txt
```

### Table Extraction

Use `tabula-py` for extracting tables:

```python
import tabula
tables = tabula.read_pdf("input.pdf", pages="all")
```

## Scripts

See `scripts/extract_text.py` for a complete extraction script.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/holsee) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
