---
name: document-generation
description: Use when generating, reviewing, or managing investment review documents, compliance submissions, or client correspondence
metadata:
  author: michaelkennedyau
---

# Document Generation

## Generating Documents

- `generate_document` — General document generation with optional template
- `generate_investment_review` — Specialised investment review document
- `brew_document` — Full review with compliance check (recommended)

## Document Lifecycle

1. **Generate**: Create the document using any of the tools above
2. **Retrieve**: `get_document` to view a previously generated document
3. **Submit**: `submit_for_review` to send for compliance review
4. **Templates**: `get_templates` to see available document templates

## Ingesting Data

- `ingest_file` — Parse CSV or XLSX files to extract holdings data
- Supports bulk upload of portfolio data for review preparation

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/michaelkennedyau) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
