---
name: pdf-generation-verification
description: Generate professional PDF documents from markdown and systematically verify formatting quality. Use when creating business proposals, reports, or formal documents requiring PDF output with proper tables, hyperlinks, and typography. Includes verification workflow to catch font warnings, table wrapping, bare URLs, and formatting issues before presenting to users. Use when this capability is needed.
metadata:
  author: tainora
---

# PDF Generation & Verification

## Overview

Generate high-quality PDF documents from markdown using pandoc with XeLaTeX, followed by systematic verification to ensure professional formatting. This skill addresses common PDF generation pitfalls: font warnings, awkward table wrapping, visible bare URLs, and undetected formatting issues.

**Key principle:** Always verify PDF output systematically before presenting to users. Reactive debugging (user finds issues → fix → repeat) wastes time and erodes confidence.

## Workflow: Generate-Verify-Fix Loop

Follow this workflow for all PDF generation tasks:

### 1. Pre-Generation: Prepare Markdown

Before generating the PDF, ensure the source markdown follows best practices:

**Check for emoji characters:**
```bash
# Search for emoji in markdown
grep -P "[\x{1F300}-\x{1F9FF}]|[\x{2600}-\x{26FF}]|[\x{2700}-\x{27BF}]" INPUT.md
```

**Action if found:** Remove emoji, replace with text bullets or standard characters (- instead of ✅)

**Check for manual section numbering:**
- ❌ Manual numbers in headings (`## 1. Introduction`)
- ✅ Use semantic levels only (`## Introduction`) + `--number-sections` flag

**Check for bare URLs:**
```bash
# Find unlinked URLs
grep -E "^https?://" INPUT.md
```

**Action if found:** Convert to markdown links: `[Descriptive Text](https://url)`

**Review table structure:**
- Avoid 3+ column tables with long text (causes wrapping)
- Consider hierarchical bullet lists for citations instead of tables
- Use 2-column tables when possible

### 2. Generate PDF

Use the standard pandoc command for business documents:

```bash
pandoc INPUT.md -o OUTPUT.pdf \
  --pdf-engine=xelatex \
  -V geometry:a4paper \
  -V geometry:margin=1in \
  -V fontsize=11pt \
  -V mainfont="DejaVu Sans" \
  -V colorlinks=true \
  -V linkcolor=blue \
  -V urlcolor=blue \
  --toc \
  --toc-depth=2 \
  2>&1 | tee /tmp/pandoc-warnings.txt
```

**Monitor font warnings in real-time:**
- Zero warnings = proceed to verification
- Font warnings present = fix before verification (see Troubleshooting)

### 3. Systematic Verification

**CRITICAL:** Do not present PDF to user without running verification.

Run the verification script:

```bash
scripts/verify-pdf.sh OUTPUT.pdf
```

The script checks:
1. PDF metadata (pages, file size, producer)
2. Bare URLs in rendered text (should be 0)
3. Table formatting and alignment
4. Text wrapping issues (excessive hyphenation)
5. Orphaned bullets or list markers
6. Document structure (sections, TOC)

**Additional quick check for bullet rendering:**
```bash
# Check for inline dashes (expect 0 matches)
pdftotext OUTPUT.pdf - | grep -E '^\w.*: -'
```

**Review the verification report:**
- ✓ marks indicate passing checks
- ⚠ marks indicate warnings requiring review
- ✗ marks indicate failures requiring fixes

### 4. Visual Confirmation

Open the PDF in a viewer to manually verify:

```bash
# macOS
open -a Skim OUTPUT.pdf

# Linux
evince OUTPUT.pdf
```

**Check these critical elements:**
- **Bullet lists render as bullets (•), not inline dashes** (CRITICAL)
- Tables render without text overflow
- All hyperlinks are clickable
- No visible bare URLs
- Font rendering is consistent
- Page breaks are appropriate
- Table of contents links work

### 5. Fix Issues and Re-verify

If verification fails or visual inspection reveals issues:

1. Identify the root cause (font, table structure, URL formatting)
2. Fix the source markdown
3. Regenerate PDF (step 2)
4. Run verification again (step 3)
5. Visually confirm (step 4)

**Do not present to user until all checks pass.**

## Font Selection

**Recommended fonts for business documents:**

1. **DejaVu Sans** (default choice)
   - Excellent Unicode coverage
   - Professional weight
   - Zero warnings for standard characters (→, ×, –)

2. **Helvetica Neue** (macOS built-in)
   - Classic professional appearance
   - Limited Unicode support

3. **Georgia** (serif option)
   - Formal documents
   - Good readability

**Font warnings to avoid:**
```
[WARNING] Missing character: There is no ✅ (U+2705)
```

**Solution:** Remove emoji from source markdown before generation.

## Table Formatting Guidelines

### When Tables Work Well

**Two-column tables with short text:**
```markdown
| Metric | Value |
|--------|-------|
| Time saved | 3000 hours/year |
| Cost | TBD |
```

**Alignment-focused tables:**
```markdown
| Phase | Timeline | Effort |
|-------|----------|--------|
| 1     | 6 months | 540h   |
| 2     | 12 months| 750h   |
```

### When to Avoid Tables

**Three+ column tables with long text:**
- Causes awkward mid-phrase wrapping
- Date columns waste horizontal space
- Description text breaks incorrectly

**Better alternative - Hierarchical bullet lists:**
```markdown
**October 2025 Reforms:**
- ["How to Prepare for the 2025 NSW Strata Law Changes"](https://...) (Oct 27, 2025)
- ["NSW Strata Law Changes 2025 – What Owners Need to Know"](https://...) (Oct 15, 2025)

**Insurance Crisis:**
- ["Preparing for 2025 Insurance Renewals"](https://...) (Oct 30, 2024) – *Budget 15-20% minimum*
```

**Advantages:**
- No text wrapping issues
- Clean visual hierarchy
- Dates in natural position
- Key insights in italics

## Hyperlinks & Citations

### Best Practices

**Embed all URLs as markdown links:**
```markdown
[Descriptive Title](https://example.com/long-url)
```

**Never use bare URLs:**
```markdown
❌ https://example.com/long-url
```

**Chronological citations format:**
```markdown
- ["Document Title"](https://url) (Oct 27, 2025) – *Key insight or context*
```

### Verification

```bash
# Check for bare URLs in generated PDF
pdftotext OUTPUT.pdf - | grep -c "https://"
```

**Expected result:** 0 (all URLs should be embedded as clickable links)

## Troubleshooting

### Bullet Lists Rendering as Inline Text (CRITICAL)

**Symptom:** Bullet lists appear as inline text with dashes instead of proper bullets (•)

```
Multi-layer validation frameworks: - HTTP/API layer validation - Schema validation...
```

**Root Cause:** LaTeX's default justified text alignment breaks Pandoc-generated bullet lists.

**Solutions:**
1. **Always use `\raggedright` in LaTeX preamble** (see references/pdf-best-practices.md)
2. Use canonical build script: `~/.claude/skills/pandoc-pdf-generation/assets/build-pdf.sh`
3. Never create ad-hoc Pandoc commands without LaTeX preamble

**Verification:**
```bash
# Check for inline dashes (expect 0 matches)
pdftotext OUTPUT.pdf - | grep -E '^\w.*: -'
```

**Why This Happens:**
- Justified text tries to make lines equal width
- LaTeX may reflow text, breaking list structures
- Lists following paragraphs with colons are especially vulnerable
- `\raggedright` disables justification, preserving formatting

**Reference:** See `~/.claude/skills/pandoc-pdf-generation/references/troubleshooting-pandoc.md` → "Bullet Lists Rendering as Inline Text" section for detailed analysis.

### Font Warnings During Generation

**Symptom:**
```
[WARNING] Missing character: There is no → (U+2192) in font Open Sans
```

**Solutions:**
1. Switch to DejaVu Sans: `-V mainfont="DejaVu Sans"`
2. Remove problematic characters from source markdown
3. Use ASCII alternatives (-> instead of →)

### Ugly Table Wrapping in PDF

**Symptom:** Date column breaks mid-line, topic text has awkward breaks

**Solutions:**
1. Convert table to hierarchical bullet list (recommended)
2. Reduce to 2-column table
3. Shorten column content
4. Use landscape orientation: `-V geometry:landscape`

### Bare URLs Visible in PDF

**Symptom:** `https://example.com/path` appears as plain text

**Solutions:**
1. Convert to markdown link: `[Title](https://example.com/path)`
2. Verify fix: `pdftotext OUTPUT.pdf - | grep "https://"`
3. Regenerate PDF

### Large File Size

**Symptom:** PDF >1MB for text-only document

**Solutions:**
1. Check for accidentally embedded images
2. Compress with Ghostscript:
```bash
gs -sDEVICE=pdfwrite -dCompatibilityLevel=1.4 \
   -dPDFSETTINGS=/ebook -dNOPAUSE -dQUIET -dBATCH \
   -sOutputFile=compressed.pdf OUTPUT.pdf
```

## Resources

### scripts/verify-pdf.sh

Comprehensive PDF verification script that checks:
- PDF metadata (pages, size, producer)
- Bare URL presence
- Table formatting issues
- Text wrapping problems
- Orphaned bullets
- Document structure

**Usage:**
```bash
scripts/verify-pdf.sh OUTPUT.pdf
```

### references/pdf-best-practices.md

Detailed reference documentation covering:
- Font selection criteria and recommendations
- Table formatting patterns and anti-patterns
- Hyperlink best practices
- Pandoc command templates and options
- Common issues with detailed solutions
- Tool installation instructions

**Load when:**
- Troubleshooting complex formatting issues
- Selecting fonts for specific requirements
- Optimizing pandoc command for specific document types

## Quick Reference

### Standard Business Document Command

```bash
pandoc INPUT.md -o OUTPUT.pdf \
  --pdf-engine=xelatex \
  -V geometry:a4paper \
  -V geometry:margin=1in \
  -V fontsize=11pt \
  -V mainfont="DejaVu Sans" \
  -V colorlinks=true \
  -V linkcolor=blue \
  -V urlcolor=blue \
  --toc \
  --toc-depth=2
```

### Verification Command

```bash
scripts/verify-pdf.sh OUTPUT.pdf
```

### Quick Checks

```bash
# Font warnings during generation
pandoc INPUT.md -o OUTPUT.pdf ... 2>&1 | grep WARNING

# Bare URLs in PDF
pdftotext OUTPUT.pdf - | grep -c "https://"

# PDF metadata
pdfinfo OUTPUT.pdf | grep -E "Pages|File size"
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tainora) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
