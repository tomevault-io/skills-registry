---
name: pdf
description: Comprehensive PDF manipulation toolkit for extracting text and tables, creating new PDFs, merging/splitting documents, and handling forms. When Claude needs to fill in a PDF form or programmatically process, generate, or analyze PDF documents at scale. Use when this capability is needed.
metadata:
  author: oferhalevi
---

# PDF Skill

This skill provides comprehensive PDF manipulation capabilities. Use it when you need to work with PDF documents programmatically.

## Capabilities

- **Extract Content**: Extract text, tables, and metadata from PDF documents
- **Create PDFs**: Generate new PDF documents from scratch using Python libraries like reportlab or fpdf2
- **Merge/Split**: Combine multiple PDFs or split a PDF into separate pages
- **Form Handling**: Fill in PDF forms programmatically
- **Text Analysis**: Analyze and process extracted text from PDFs
- **Document Processing**: Handle large-scale PDF processing tasks

## Tools and Libraries

Common Python libraries for PDF work:
- **reportlab**: Create PDFs from scratch with precise control
- **fpdf2**: Simple PDF generation
- **PyPDF2**: Merge, split, and manipulate existing PDFs
- **pdfplumber**: Extract text and tables from PDFs
- **python-pdf**: General PDF utilities

## When to Use This Skill

Use this skill when:
- User asks to create a PDF document
- User needs to extract information from a PDF
- User wants to merge or split PDF files
- User needs to fill PDF forms programmatically
- User wants to analyze PDF content at scale

## Best Practices

1. Always validate that the PDF file exists before processing
2. Handle errors gracefully when PDFs are corrupted or in unexpected formats
3. For large PDFs, consider processing in chunks
4. Use appropriate libraries based on the task (creation vs. manipulation vs. extraction)
5. Preserve document structure and formatting when possible

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/oferhalevi) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
