---
name: docsuite
description: > Use when this capability is needed.
metadata:
  author: kevcofett
---

# Document Suite

## Commands

- `/docs docx <title>` — Create a Word document
- `/docs xlsx <title>` — Create an Excel spreadsheet
- `/docs pptx <title>` — Create a PowerPoint presentation
- `/docs pdf <title>` — Create a PDF document
- `/docs <title>` — Auto-detect best format from description

## Format Selection

- Reports, proposals, SOWs — .docx
- Data tables, comparisons, tracking — .xlsx
- Presentations, stakeholder updates — .pptx
- Contracts, specs, formal deliverables — .pdf

## Procedure

### Phase 1: Content Gathering

1. Ask what the document is about
2. Determine audience (internal, client, executive, technical)
3. Confirm format if not specified

### Phase 2: Structure

DOCX: Title page, TOC, executive summary, body with H1/H2/H3, tables, page numbers.

XLSX: Summary sheet first, data sheets with frozen headers, consistent formatting, named ranges.

PPTX: Title slide, agenda, content slides (max 5 bullets, max 7 words each), data viz, summary slide.

PDF: Same as DOCX structure, all fonts embedded, hyperlinks preserved.

### Phase 3: Style Rules

- No emojis anywhere
- No bullet points in prose paragraphs
- Professional enterprise tone
- Sans-serif fonts (Calibri, Arial, Helvetica)
- Consistent heading styles throughout

### Phase 4: Generation

Use python-docx, openpyxl, python-pptx, or reportlab. Save to docs/output/ by default.

### Phase 5: Review

Present structure summary before generating. After generation, report file path and size.

## MCMAP Templates

- Deployment Checklist — DOCX with pass/fail verification table
- Agent Roster — XLSX with all 12 agents, GUIDs, models, domains
- Solution Release Notes — DOCX with version history, changes, known issues
- Client Presentation — PPTX for Mastercard/BMO stakeholder updates

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kevcofett) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
