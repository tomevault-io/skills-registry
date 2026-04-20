---
name: ms-office-suitepptx
description: Use this skill any time a .pptx file is involved in any way — as input, output, or both. This includes: creating slide decks, pitch decks, or presentations; reading, parsing, or extracting text from any .pptx file; editing, modifying, or updating existing presentations; combining or splitting slide files; working with templates, layouts, speaker notes, or comments.
metadata:
  author: prof-ramos
---

# PPTX Skill

## Quick Reference

| Task | Guide |
|------|-------|
| Read/analyze content | `python -m markitdown presentation.pptx` |
| Edit or create from template | Read editing.md |
| Create from scratch | Read pptxgenjs.md |

---

## Reading Content

```bash
# Text extraction
python -m markitdown presentation.pptx

# Visual overview
python scripts/thumbnail.py presentation.pptx

# Raw XML
python scripts/office/unpack.py presentation.pptx unpacked/
```

---

## Design Ideas

**Don't create boring slides.** Plain bullets on a white background won't impress anyone.

### Before Starting

- **Pick a bold, content-informed color palette**
- **Dominance over equality**: One color should dominate (60-70%)
- **Dark/light contrast**: Dark backgrounds for title + conclusion slides
- **Commit to a visual motif**: Pick ONE distinctive element and repeat it

### Typography

| Element | Size |
|---------|------|
| Slide title | 36-44pt bold |
| Section header | 20-24pt bold |
| Body text | 14-16pt |
| Captions | 10-12pt muted |

### Avoid (Common Mistakes)

- **Don't repeat the same layout** — vary columns, cards, and callouts
- **Don't center body text** — left-align paragraphs and lists
- **Don't default to blue** — pick colors that reflect the specific topic
- **Don't create text-only slides** — add images, icons, charts
- **NEVER use accent lines under titles** — hallmark of AI-generated slides

---

## QA (Required)

**Assume there are problems. Your job is to find them.**

### Content QA

```bash
python -m markitdown output.pptx
```

### Visual QA

Convert slides to images, then inspect with subagents:

```bash
python scripts/office/soffice.py --headless --convert-to pdf output.pptx
pdftoppm -jpeg -r 150 output.pdf slide
```

---

## Dependencies

- `pip install "markitdown[pptx]"` - text extraction
- `pip install Pillow` - thumbnail grids
- `npm install -g pptxgenjs` - creating from scratch
- LibreOffice (`soffice`) - PDF conversion
- Poppler (`pdftoppm`) - PDF to images

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/prof-ramos) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
