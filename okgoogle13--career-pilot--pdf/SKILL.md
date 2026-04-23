---
name: pdf-text-extractor
description: Extracts text content from one or more PDF documents.
metadata:
  author: okgoogle13
---

# Skill: PDF Text Extractor

This skill uses Claude's multimodal (vision) capabilities to read and process PDF documents. It can be used for full text extraction, summarization, Q&A, or structured data extraction.

## Agent Call

Called by: `doc-processor-agent` (or any other agent)
Input: `$PROMPT` (what to do) and one or more PDF files.

## Output

Returns a JSON object containing the processed output for each document, or an error object.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/okgoogle13) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
