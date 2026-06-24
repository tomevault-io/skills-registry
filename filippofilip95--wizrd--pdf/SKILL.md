---
name: pdf
description: Generate branded PDFs from markdown files. Use when converting case studies, proposals, or documentation to PDF format. Handles styling, templates, and batch conversion. Use when this capability is needed.
metadata:
  author: filippofilip95
---

# PDF Generator Skill

Generate professionally branded PDFs from markdown documents.

## Trigger
User runs `/pdf [file]` or asks to "generate PDF", "convert to PDF", or "create PDF".

## Usage

```bash
# Single file
/pdf content/case-studies/project.md

# With custom output
/pdf proposal.md ~/Desktop/client-proposal.pdf

# HTML (for browser printing)
/pdf --html document.md
```

## Workflow

### 1. Validate Input
- Check file exists
- Verify it's a markdown file
- Check PDF generator is set up

### 2. Generate PDF
Run the generator script:
```bash
./tools/pdf-generator/generate-pdf.sh [input] [output]
```

### 3. Report Results
- Confirm PDF location
- Open PDF (on macOS)
- Report any errors

## Common Tasks

### Convert Case Study
```bash
./tools/pdf-generator/generate-pdf.sh content/case-studies/[name].md
```
Output: `tools/pdf-generator/output/[name].pdf`

### Convert Proposal
```bash
./tools/pdf-generator/generate-pdf.sh clients/[client]/proposal.md
```

### Batch Convert
```bash
./tools/pdf-generator/generate-pdf.sh --batch content/case-studies/
```

### HTML Fallback
If no PDF engine is installed:
```bash
./tools/pdf-generator/generate-pdf.sh --html document.md
```
Then print to PDF from browser.

## Customization

### Brand Colors
Edit `tools/pdf-generator/templates/styles.css`:
```css
:root {
  --primary: #552cd5;    /* Your brand color */
  --accent: #0ea5e9;     /* Links, highlights */
}
```

### Footer
Edit `tools/pdf-generator/templates/template.html` to add:
- Company name
- Website
- Contact info

## Requirements

- **Pandoc**: `brew install pandoc`
- **WeasyPrint**: `pip install weasyprint` (recommended)

Install with:
```bash
./tools/pdf-generator/generate-pdf.sh --install
```

## Troubleshooting

### "No PDF engine found"
Install WeasyPrint: `pip install weasyprint`
Or use `--html` flag and print from browser.

### "pandoc not found"
Install: `brew install pandoc`

### Fonts not rendering
WeasyPrint needs fonts installed locally. Use system fonts or install Google Fonts.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/filippofilip95) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
