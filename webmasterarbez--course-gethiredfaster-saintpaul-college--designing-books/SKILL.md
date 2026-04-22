---
name: designing-books
description: Design and produce professional print-ready books from manuscripts. Use when creating book interiors, cover designs, typesetting memoirs or life stories, preparing PDFs for print-on-demand services (KDP, IngramSpark, Lulu), or when the user mentions book layout, typography, trim sizes, or print production. Use when this capability is needed.
metadata:
  author: webmasterarbez
---

# Book Design & Production

Transform manuscripts into professionally designed, print-ready books.

## Quick Start

1. **Determine book specifications** → See [Trim Sizes & Margins](#trim-sizes--margins)
2. **Set up typography** → See [Typography System](#typography-system)
3. **Create interior layout** → See [Interior Design Workflow](#interior-design-workflow)
4. **Generate print-ready PDF** → See [PDF Generation](#pdf-generation)
5. **Validate for printing** → See [Print Preflight Checklist](#print-preflight-checklist)

## Core Concepts

### The Production Pipeline

```
Manuscript (.md/.docx) → Typeset Layout → Interior PDF → Print-Ready Package
                                       ↘ Cover PDF  ↗
```

### Key Terminology

- **Trim size**: Final book dimensions after cutting
- **Bleed**: 0.125" extra beyond trim for edge-to-edge elements
- **Gutter**: Inside margin (binding side) - larger for perfect binding
- **Spine width**: Calculated from page count and paper stock
- **Widow/Orphan**: Single lines isolated at page top/bottom (avoid)
- **Running head**: Repeated header showing chapter/book title
- **Folio**: Page number

## Trim Sizes & Margins

### Common Memoir/Biography Sizes

| Size Name | Dimensions | Best For |
|-----------|------------|----------|
| Trade Paperback | 6" × 9" | Standard memoirs, biographies |
| Digest | 5.5" × 8.5" | Personal memoirs, gift books |
| US Letter | 8.5" × 11" | Photo-heavy memoirs, legacy books |
| Royal | 6.14" × 9.21" | Premium memoirs |
| Small Square | 7.5" × 7.5" | Photo memoirs, coffee table |

### Standard Margin Guidelines

For **6" × 9" trade paperback** (memoir standard):

```
Top margin:     0.75"
Bottom margin:  0.875"
Outside margin: 0.625"
Inside (gutter): 0.875" (for perfect binding up to 400 pages)
```

**Gutter adjustments by page count:**
- Under 150 pages: 0.75" gutter
- 150-400 pages: 0.875" gutter
- 400-600 pages: 1.0" gutter
- Over 600 pages: 1.125" gutter

## Typography System

### Font Selection for Memoirs

**Body Text (Serif recommended):**
- **Garamond** - Classic, elegant, excellent readability
- **Palatino** - Warm, humanist, good for personal stories
- **Minion Pro** - Modern classic, superb legibility
- **Caslon** - Traditional, trustworthy feel
- **Georgia** - Screen-optimized, works well in print

**Chapter Headings:**
- Same serif family (larger weight)
- Or complementary sans-serif (Helvetica, Gill Sans, Futura)

### Type Specifications

**Body text:**
- Size: 11-12pt (10.5pt minimum for older readers)
- Leading: 14-15pt (roughly 120-140% of font size)
- Justified alignment with hyphenation
- First-line indent: 0.25" to 0.375" (no indent after headings)

**Chapter titles:**
- Size: 18-24pt
- Position: Start 2-3" from top of page
- Style: Small caps, bold, or display weight

**Running headers:**
- Size: 9-10pt
- Style: Small caps or italic
- Left page: Book title | Right page: Chapter title

**Folios (page numbers):**
- Size: 10pt
- Position: Bottom center or outside corners
- Front matter: Lowercase roman numerals (i, ii, iii)
- Body: Arabic numerals starting at Chapter 1

## Interior Design Workflow

### Step 1: Prepare the Manuscript

```bash
# If starting from DOCX, convert to clean markdown
pandoc input.docx -o manuscript.md --wrap=none

# Clean up common issues
# - Standardize chapter headings (# Chapter 1: Title)
# - Remove manual line breaks within paragraphs
# - Ensure consistent scene break markers (---)
```

### Step 2: Create Book Structure

Standard memoir front matter:
1. Half-title page (title only, no author)
2. Also by page (optional)
3. Title page (full title, author, publisher)
4. Copyright page
5. Dedication (optional)
6. Epigraph (optional)
7. Table of contents
8. Preface/Introduction (optional)

Back matter:
1. Acknowledgments
2. About the Author
3. Discussion questions (optional)
4. Index (if needed)

### Step 3: Set Up Page Templates

See `./reference/page-templates.md` for detailed LaTeX/CSS templates.

**Key templates needed:**
- Chapter opener (right-hand page start, drop cap optional)
- Standard text page
- Section break page
- Photo page (full bleed or framed)
- Front matter pages (centered, no headers)

### Step 4: Handle Special Elements

**Block quotes:**
- Indent 0.5" from both margins
- Reduce font size by 1pt
- Add 6pt space above and below

**Poetry/Verse:**
- Preserve line breaks
- Center or left-align based on length
- Consider reduced font size for long poems

**Photos and Images:**
- Resolution: 300 DPI minimum
- Color space: CMYK for full color, Grayscale for B&W
- Captions: 9pt italic, centered below image

**Letters/Documents:**
- Consider different font (typewriter style)
- Frame with subtle border or indent
- Date/salutation on separate lines

## PDF Generation

### Using Pandoc + LaTeX (Recommended)

```bash
# Install dependencies
npm install -g @anthropic/book-utils  # If available
# Or use system packages:
# sudo apt-get install texlive-full pandoc

# Generate interior PDF
pandoc manuscript.md \
  -o interior.pdf \
  --pdf-engine=xelatex \
  --template=memoir-template.tex \
  -V documentclass=memoir \
  -V papersize=6in,9in \
  -V fontsize=11pt \
  -V mainfont="Garamond" \
  --toc \
  --toc-depth=1
```

See `./reference/pandoc-settings.md` for complete configuration.

### Using Node.js + PDF Libraries

```javascript
// See scripts/generate-book-pdf.js for complete implementation
import { PDFDocument } from 'pdf-lib';
import fontkit from '@pdf-lib/fontkit';

// Create document with proper trim size
const pdfDoc = await PDFDocument.create();
pdfDoc.registerFontkit(fontkit);

// 6x9 trim size in points (72 points per inch)
const width = 6 * 72;  // 432 points
const height = 9 * 72; // 648 points

const page = pdfDoc.addPage([width, height]);
```

### Cover Creation

**Spine width calculation:**
```
Spine Width = (Page Count × Paper Thickness) + Cover Wrap
```

Paper thickness by stock:
- White paper: 0.002252" per page
- Cream/natural: 0.0025" per page

**Full cover dimensions:**
```
Width = Back Cover + Spine + Front Cover + (2 × Bleed)
Height = Trim Height + (2 × Bleed)

Example for 200-page 6×9 book on cream paper:
Spine = 200 × 0.0025 = 0.5"
Width = 6 + 0.5 + 6 + 0.25 = 12.75"
Height = 9 + 0.25 = 9.25"
```

See `./reference/cover-templates.md` for cover design guidelines.

## Print Preflight Checklist

Before submitting to printer:

### Technical Requirements
- [ ] PDF/X-1a or PDF/X-4 compliance
- [ ] All fonts embedded (no system fonts)
- [ ] Images at 300 DPI minimum
- [ ] Color space correct (CMYK for color, Grayscale for B&W)
- [ ] Bleed: 0.125" on all sides where needed
- [ ] Trim marks included (if required by printer)

### Layout Quality
- [ ] No widows or orphans
- [ ] Consistent baseline grid
- [ ] Chapter openings on recto (right) pages
- [ ] Page numbers correct sequence
- [ ] Running headers match chapter titles
- [ ] Table of contents page numbers accurate

### Content Proofing
- [ ] Front matter complete and ordered
- [ ] Copyright page accurate
- [ ] ISBN displayed correctly (if applicable)
- [ ] All images credited
- [ ] Index accurate (if included)

### File Delivery
- [ ] Interior PDF separate from cover PDF
- [ ] Files named per printer requirements
- [ ] Page count divisible by 4 (add blanks if needed)
- [ ] Cover dimensions match spine calculation

## Print-on-Demand Services

### Amazon KDP (Kindle Direct Publishing)

```yaml
Interior requirements:
  format: PDF
  bleed: Optional (0.125" if used)
  color: Black & white or Premium color
  paper: White or cream

Cover requirements:
  format: PDF
  bleed: 0.125" required
  color: Full color
  spine_calculator: https://kdp.amazon.com/cover-calculator
```

### IngramSpark

```yaml
Interior requirements:
  format: PDF/X-1a:2001
  bleed: 0.125" required for bleed books
  minimum_margins: 0.25" (0.375" for gutter)

Cover requirements:
  format: PDF/X-1a:2001
  spine_calculator: Available in publisher portal
```

### Lulu

```yaml
Interior requirements:
  format: PDF
  bleed: 0.125" if full-bleed images
  color: Standard B&W, Premium B&W, or Color

Cover requirements:
  format: PDF or use cover creator
  spine: Auto-calculated in creator tool
```

## Reference Documentation

- `./reference/page-templates.md` - LaTeX and CSS page templates
- `./reference/pandoc-settings.md` - Complete Pandoc configuration
- `./reference/cover-templates.md` - Cover design specifications
- `./reference/typography-guide.md` - Detailed typography reference
- `./templates/memoir-template.tex` - LaTeX memoir template
- `./scripts/generate-book-pdf.js` - Node.js PDF generation

## Common Issues & Solutions

### Widow/Orphan Control

```latex
% LaTeX settings
\widowpenalty=10000
\clubpenalty=10000
```

If still occurring, manually adjust:
- Tighten/loosen paragraph tracking
- Edit text slightly
- Force page breaks strategically

### Font Embedding Failures

```bash
# Check font embedding
pdffonts interior.pdf

# If fonts not embedded, use XeLaTeX with system fonts
# or embed via Ghostscript:
gs -dNOPAUSE -dBATCH -sDEVICE=pdfwrite \
   -dEmbedAllFonts=true \
   -sOutputFile=embedded.pdf interior.pdf
```

### Image Quality Issues

```bash
# Check image resolution
identify -verbose image.jpg | grep Resolution

# Resample to 300 DPI if needed (ImageMagick)
convert image.jpg -resample 300 image-300dpi.jpg

# Convert RGB to CMYK for print
convert image.jpg -colorspace CMYK image-cmyk.jpg
```

### Page Count Adjustment

Total pages must be divisible by 4. Add blank pages at end if needed:

```bash
# Using pdftk
pdftk interior.pdf blank.pdf blank.pdf cat output final.pdf
```

## Memoir-Specific Design Tips

### For Life Story Books (Eleanor-style)

1. **Generous margins** - Older readers appreciate more white space
2. **Larger body text** - 12pt minimum for senior audiences
3. **Photo integration** - Full-page photos with captions, or grouped in signatures
4. **Chapter art** - Consider decorative elements (flourishes, small illustrations)
5. **Personal touches** - Handwriting fonts for inscriptions, family crests/symbols
6. **Archival quality** - Cream paper, acid-free recommendations for keepsake books

### Photo Memoir Considerations

- Group photos into 8-page or 16-page signatures
- Consider photo paper inserts for premium look
- Caption style: Name, date, location format
- Photo restoration notes if applicable

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/webmasterarbez) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
