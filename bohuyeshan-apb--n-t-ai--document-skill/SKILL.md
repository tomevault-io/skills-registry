---
name: document-generation
description: Use when working with a powerful skill for generating and processing professional documents (Word, PowerPoint, Excel, PDF).
metadata:
  author: bohuyeshan-apb
---

# Document Generation & Processing Skill

This skill provides comprehensive capabilities for creating, editing, and extracting content from office documents. It is designed to be used by agents requiring document manipulation capabilities.

## Capabilities

### 1. Document Generation
- **Word (.docx)**: Generate professional reports with headings, paragraphs, bullet points, and tables.
- **PowerPoint (.pptx)**: Create slide decks with titles, content, and consistent layouts.
- **Excel (.xlsx)**: Create spreadsheets with data and basic formatting.
- **PDF (.pdf)**: Generate PDF documents from text content.

### 2. Document Processing
- **Content Extraction**: Extract text and structure from uploaded PDF, Word, PowerPoint, and Excel files.
- **Format Conversion**: Convert between compatible formats.

## Usage

This skill exposes a set of Python scripts located in the `scripts/` directory.

### Python API

```python
from app.skills.common.document_skill.scripts.word_generator import WordGenerator
from app.skills.common.document_skill.scripts.ppt_generator import PPTGenerator
from app.skills.common.document_skill.scripts.excel_generator import excel_generator
from app.skills.common.document_skill.scripts.office_processor import OfficeProcessor

# Example: Generate a Word Document
word_gen = WordGenerator(output_dir="path/to/output")
file_path = word_gen.generate(markdown_content, "filename")

# Example: Extract content
processor = OfficeProcessor()
content = processor.process("path/to/file.pdf")
```

## Dependencies
- python-docx
- python-pptx
- openpyxl
- reportlab (for PDF)
- pypdf (for PDF extraction)

## Directory Structure
- `scripts/`: Implementation logic.
- `templates/`: (Optional) Document templates.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/bohuyeshan-apb) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
