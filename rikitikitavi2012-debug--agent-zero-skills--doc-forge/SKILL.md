---
name: doc-forge
description: Document generation and formatting toolkit. Create PDF, DOCX, PPTX documents from templates or markdown. Convert between formats, generate reports, create presentations. Use when user needs document creation, formatting, or conversion. Use when this capability is needed.
metadata:
  author: rikitikitavi2012-debug
---

# Doc Forge — Document Generation

Create professional documents from markdown or templates. Supports PDF, DOCX, and PPTX formats with customizable styling.

## Installation

```bash
# Install dependencies
pip install -r requirements.txt

# Or use setup script
bash /a0/usr/skills/setup.sh

# Note: weasyprint requires system dependencies on some Linux distributions:
# Debian/Ubuntu: apt-get install libpango-1.0-0 libharfbuzz0b libpangoft2-1.0-0
# AlmaLinux/RHEL: yum install pango pango-devel
```

## When to Use

Use this skill when you need to:
- Create PDF documents from markdown
- Generate DOCX reports
- Create PowerPoint presentations
- Convert between document formats
- Apply templates to structured data

## Supported Formats

| Format | Extension | Features |
|--------|-----------|----------|
| **PDF** | .pdf | Print-ready, styled with CSS |
| **DOCX** | .docx | Editable Word documents |
| **PPTX** | .pptx | PowerPoint presentations |

## Usage

### Via Python Script
```bash
python /a0/usr/skills/doc-forge/scripts/doc_forge.py --format pdf --input document.md --output report.pdf --title "Report Title"
```

### Parameters

| Parameter | Type | Default | Description |
|-----------|------|---------|-------------|
| `--format` | str | required | Output format: pdf/docx/pptx |
| `--input` | str | required | Input markdown file path |
| `--output` | str | required | Output file path |
| `--title` | str | "Document" | Document title |
| `--template` | str | optional | Template file path |

### Examples

1. **PDF from markdown:**
   ```bash
   python /a0/usr/skills/doc-forge/scripts/doc_forge.py --format pdf --input report.md --output report.pdf --title "Q4 Report"
   ```

2. **DOCX document:**
   ```bash
   python /a0/usr/skills/doc-forge/scripts/doc_forge.py --format docx --input notes.md --output notes.docx --title "Meeting Notes"
   ```

3. **PowerPoint presentation:**
   ```bash
   python /a0/usr/skills/doc-forge/scripts/doc_forge.py --format pptx --input slides.md --output presentation.pptx --title "Project Proposal"
   ```

## Markdown Support

- Headers (H1-H6)
- Lists (ordered/unordered)
- Tables
- Code blocks
- Bold/italic text
- Links
- Images

## Requirements

- `markdown>=3.5.0`
- `python-docx>=1.1.0`
- `python-pptx>=0.6.23`
- `weasyprint>=60.0`
- `Jinja2>=3.1.0`
- `Pillow>=10.0.0`

## Files

```
/a0/usr/skills/doc-forge/
├── scripts/
│   └── doc_forge.py
├── requirements.txt
└── SKILL.md
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rikitikitavi2012-debug) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
