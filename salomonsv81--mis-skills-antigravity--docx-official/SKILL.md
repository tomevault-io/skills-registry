---
name: docx
description: Comprehensive document creation, editing, and analysis with support for tracked changes, comments, formatting preservation, and text extraction. When Claude needs to work with professional documents (.docx files). Use when this capability is needed.
metadata:
  author: salomonsv81
---

# DOCX Creation, Editing, and Analysis

## Overview

A user may ask you to create, edit, or analyze the contents of a .docx file. A .docx file is essentially a ZIP archive containing XML files and other resources that you can read or edit.

## Workflow Decision Tree

### Reading/Analyzing Content
- **Text extraction**: Use pandoc to convert to markdown
```bash
pandoc --track-changes=all path-to-file.docx -o output.md
```

### Creating New Document
Use **docx-js** (JavaScript/TypeScript library)

### Editing Existing Document
- **Your own document + simple changes**: Basic OOXML editing
- **Someone else's document**: Use redlining workflow
- **Legal/academic/business docs**: Use redlining workflow (required)

## Key File Structures

- `word/document.xml` - Main document contents
- `word/comments.xml` - Comments referenced in document.xml
- `word/media/` - Embedded images and media files

## Dependencies

- **pandoc**: For text extraction
- **docx**: npm package for creating new documents
- **LibreOffice**: For PDF conversion
- **Poppler**: For pdftoppm to convert PDF to images

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/salomonsv81) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
