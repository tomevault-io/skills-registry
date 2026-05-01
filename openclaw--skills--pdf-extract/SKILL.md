---
name: pdf-extract
description: Extract text from PDF files for LLM processing Use when this capability is needed.
metadata:
  author: openclaw
---

# PDF Extract

Extract text from PDF files for LLM processing. Uses `pdftotext` from the poppler-utils package to convert PDF documents into plain text.

## Commands

```bash
# Extract all text from a PDF
pdf-extract "document.pdf"

# Extract text from specific pages
pdf-extract "document.pdf" --pages 1-5
```

## Install

```bash
sudo dnf install poppler-utils
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
