---
name: manuscript-generator
description: Generate academic paper drafts from project code, supporting bilingual Chinese/English output Use when this capability is needed.
metadata:
  author: asdfgtrewq748
---

# Manuscript Generator Skill

## Description

This skill generates academic paper drafts from your project code, supporting both Chinese and English versions. It analyzes your Python codebase to extract technical content and generates well-formatted Word documents (.docx) using the provided template.

## Features

- **Code Analysis**: Automatically analyzes Python modules, classes, and functions
- **Template Matching**: Uses your existing manuscript template for consistent formatting
- **Bilingual Support**: Generates both Chinese and English versions
- **Structured Output**: Creates papers with standard sections:
  - Introduction
  - Theoretical Background
  - Proposed Method
  - System Implementation
  - Results and Discussion
  - Conclusion

## Usage

Invoke this skill when you want to generate academic paper drafts from your project:

```
/manuscript-generator
```

### Parameters

- `project_root` (optional): Path to project directory (default: current directory)
- `template_path` (optional): Path to template docx file (default: Manuscript .docx)
- `output_dir` (optional): Output directory (default: output)
- `title` (optional): Paper title in English
- `language` (optional): "cn", "en", or "both" (default: both)

### Example

```
Generate a paper draft for the current project using the template at "Manuscript .docx".
Output both Chinese and English versions to the "output" directory.
```

## Output

The skill generates two Word documents:
- `Manuscript_Chinese.docx` - Chinese version
- `Manuscript_English.docx` - English version

## Requirements

- python-docx library (install with: `pip install python-docx`)

## Implementation Details

The generator works by:
1. Scanning all Python files in the project
2. Extracting classes, functions, and docstrings
3. Identifying algorithms and key modules
4. Generating structured content based on analysis
5. Applying template formatting with python-docx

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/asdfgtrewq748) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
