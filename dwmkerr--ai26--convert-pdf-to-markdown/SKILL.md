---
name: convert-pdf-to-markdown
description: This skill should be used when the user asks to "convert a PDF to markdown", "extract text from PDF", or mentions PDF to markdown conversion. Use when this capability is needed.
metadata:
  author: dwmkerr
---

# Convert PDF to Markdown

Convert PDF files to markdown using the AI26 backoffice conversion service.

## When asking for filename

If the user hasn't specified a PDF file:
- Suggest any PDF files mentioned in the conversation
- Otherwise use "doc.pdf" as the example with description "Path to your PDF document"

## Usage

Run the conversion script:

```bash
${CLAUDE_PLUGIN_ROOT}/scripts/pdf-to-markdown.sh <filename.pdf>
```

## Response Format

The script returns JSON with `content` (markdown) and `mimetype` fields. Extract and display the `content` field to the user.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/dwmkerr) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
