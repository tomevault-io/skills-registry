---
name: mip-pdf
description: Phase 7 of MIP pipeline - Generate final PDF document from the essay and appendix using pandoc. Use when this capability is needed.
metadata:
  author: gjohnsx
---

# MIP PDF Generation Phase

Generate the final PDF document from the completed essay and appendix.

## Your Task

Use pandoc to convert the markdown files into a professionally formatted PDF.

## Prerequisites Check

Before generating, verify:
1. `mip-analysis/essay/analysis.md` exists and is complete
2. `mip-analysis/essay/appendix.md` exists
3. `mip-analysis/essay/verification-report.md` confirms all quotes verified
4. pandoc is installed (`which pandoc`)
5. A LaTeX engine is available (xelatex preferred)

## PDF Generation Command

```bash
pandoc mip-analysis/essay/analysis.md mip-analysis/essay/appendix.md \
  -o mip-analysis/essay/analysis.pdf \
  --pdf-engine=xelatex \
  -V geometry:margin=1in \
  -V fontsize=12pt \
  -V linestretch=2 \
  -V mainfont="Times New Roman" \
  --toc \
  --toc-depth=2 \
  -V colorlinks=true \
  -V linkcolor=blue
```

## Fallback Options

If xelatex is not available:
```bash
# Try pdflatex
pandoc mip-analysis/essay/analysis.md mip-analysis/essay/appendix.md \
  -o mip-analysis/essay/analysis.pdf \
  --pdf-engine=pdflatex \
  -V geometry:margin=1in \
  -V fontsize=12pt

# Or use wkhtmltopdf via HTML
pandoc mip-analysis/essay/analysis.md mip-analysis/essay/appendix.md \
  -o mip-analysis/essay/analysis.html && \
wkhtmltopdf mip-analysis/essay/analysis.html mip-analysis/essay/analysis.pdf
```

## PDF Specifications

Target format:
- **Margins**: 1 inch all sides
- **Font**: 12pt, Times New Roman or similar serif
- **Line spacing**: Double-spaced
- **Table of Contents**: Yes, 2 levels deep
- **Page numbers**: Bottom center
- **Header**: Essay title

## Process

1. Check prerequisites
2. Run pandoc command
3. Verify PDF created successfully
4. Check PDF page count (expect 12-16 pages)
5. Open or display PDF location

## Error Handling

If pandoc fails:
1. Check for LaTeX errors in output
2. Try alternative PDF engine
3. Verify markdown syntax is valid
4. Check for special characters that need escaping

Common fixes:
- Escape `$` symbols: `\$`
- Ensure code blocks are properly fenced
- Check table formatting

## Success Criteria

- PDF generated at `mip-analysis/essay/analysis.pdf`
- PDF opens without errors
- All content from both markdown files included
- Table of contents present
- Formatting professional and readable
- Pipeline complete!

## Final Output Summary

After successful PDF generation, report:

```
MIP Pipeline Complete!

Output files:
├── mip-analysis/
│   ├── research-notes.md
│   ├── annotations/
│   │   └── chapter-01.json ... chapter-09.json
│   ├── cmt-systems.json
│   ├── cmt-summary.md
│   ├── outline.md
│   └── essay/
│       ├── analysis.md
│       ├── appendix.md
│       ├── verification-report.md
│       └── analysis.pdf  ← FINAL OUTPUT

Total metaphors analyzed: [N]
CMT systems identified: 4
Essay word count: ~3250
PDF pages: [N]
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/gjohnsx) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
