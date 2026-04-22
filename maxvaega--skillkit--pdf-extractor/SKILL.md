---
name: pdf-extractor
description: Extract and convert PDF documents using Python scripts Use when this capability is needed.
metadata:
  author: maxvaega
---

# PDF Extractor Skill

This skill provides tools for extracting text and metadata from PDF documents and converting them to different formats.

## Available Scripts

### extract.py
Extracts text and metadata from PDF files.

**Input**:
```json
{
  "file_path": "/path/to/document.pdf",
  "pages": "all" | [1, 2, 3]
}
```

**Output**:
```json
{
  "text": "Extracted text content...",
  "metadata": {
    "title": "Document Title",
    "author": "Author Name",
    "pages": 10
  }
}
```

### convert.sh
Converts PDF files to different formats (text, markdown, etc.).

**Input**:
```json
{
  "input_file": "/path/to/input.pdf",
  "output_format": "txt" | "md" | "html"
}
```

### parse.py
Parses structured data from PDF forms and tables.

**Input**:
```json
{
  "file_path": "/path/to/form.pdf",
  "extract_tables": true,
  "extract_forms": true
}
```

## Usage Example

```python
from skillkit import SkillManager

manager = SkillManager()
result = manager.execute_skill_script(
    skill_name="pdf-extractor",
    script_name="extract",
    arguments={"file_path": "document.pdf", "pages": "all"}
)

if result.success:
    print(result.stdout)
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/maxvaega) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
