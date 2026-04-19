---
name: qmd-slides-reviewer
description: Review Quarto `.qmd` slides for formatting guidelines, sanity checks, and layout best practices. Use when the user asks to review, validate, check, or ensure quality of `.qmd` slide files. Use when this capability is needed.
metadata:
  author: hassanalgoz
---

# Review

- Review your work to make sure you have no duplication of examples (if so, merge them).
- Review also for formatting guidelines (below)

## Content Review

**1. Visual Reference Updates**

* **Target Images:** Locate images which do not yet exist in the `assets/` folder they are referencing.
* **Format:** Update their prompts using the following syntax: `![prompt: [Detailed Description] style: [Insert Style Name]]`. Style should be from @style.md

**2. Speaker Notes Enrichment**

* **Narrative Flow:** Rewrite the speaker notes to ensure a smooth, logical transition between points, maintaining a cohesive narrative voice throughout the presentation.

## `.qmd` Slides Formatting Guidelines

## 0.1 Output sanity checks (run these before finalizing)

**Slides**
- No slide separators in the body:
  - do not use `---` to separate slides
  - do not use `---` as a horizontal rule
- Slides are created by headings only:
  - `#` is used only for: Day / Session / Break / Hands-on
  - all other slides use `##`
  - do not use `###` headings anywhere
- There is an empty line before every slide heading line (`# ...` or `## ...`).
- Every slide has exactly one clear title (its heading).
- No slide requires scrolling (split / columns / `{.smaller}`).

**Code fences**
- Every code block is properly closed with matching backticks.
- Never leave a code block open right before a new slide heading.

**Notebook blocks**
- Bootcamp blocks are valid HTML comments and valid YAML mappings.
- Exercise IDs are valid Python identifiers and unique in the deck.
- `check` and `hidden_check` are pure Python (no notebook magics).

## 1) Slide mechanics

### 1.1 How slides are created (no `---`)
- Do not separate slides with `---`.
- A new slide is created by a heading:
  - `# ...` for divider slides (restricted; see below)
  - `## ...` for all content slides
- Do not use `###` headings anywhere in a deck. Use bold text instead.

### 1.3 Blank line before every slide title (required)
There must be an empty line before every slide heading line:
- before every `# ` line
- before every `## ` line

### 1.4 `---` is reserved for YAML front matter only
The only place `---` may appear (unfenced) is the YAML front matter at the top of the file.  
If you need to show `---` as text, put it inside a fenced code block.

### 1.5 Do not use `.muted`
Do not use `::: {.muted}` or `.muted` anywhere.

---

## 2) Output Format

- Format: `.qmd` (Quarto Markdown) which are the presentation slides (revealjs format).
- Output a separate `.qmd` file for each session of the module, beause otherwise, the slides will be too long and the trainer will have to scroll through them. Keep an `index.qmd` for each module that lists all the sessions in the module for easy navigation.


## 3) A second pass review to ensure the following

### A. No scrolling (almost always)
- Split content into more slides rather than using `.scrollable`.
- Use columns to fit “concept + example” side-by-side.

This is an example of how Quarto does a two column layout:

```qmd
::: {layout-ncol=2}
### Good for

- Problems with long lists of rules
- Continually changing environments
- Discovering insights in large data collections

### Not good for:

- When you need explainability
- When simple rules work better
- When errors are unacceptable
- When you don't have much data
:::
```

### B. PDF-friendly
- Slides must export cleanly to PDF.
- Avoid heavy full-bleed dark backgrounds except for rare divider slides.
- Prefer high-contrast diagrams and minimal screenshots.

### C. Images

- If the text on the slide is too long, use `{.smaller}` to make the text smaller.
- If the image can fit, you may use a two-column layout and the dimensions of the image is vertically tall to fit the image and the text side by side.
- If the image is horizontally wide, you may use a one-column layout and the dimensions of the image is horizontally wide to fit the image and the text side by side.
- If the text is just too much and can't fit even if using smaller, you have to put the image on a separate slide.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hassanalgoz) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
