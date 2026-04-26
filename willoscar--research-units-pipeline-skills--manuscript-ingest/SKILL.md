---
name: manuscript-ingest
description: | Use when this capability is needed.
metadata:
  author: willoscar
---

# Manuscript Ingest (paper -> text)

Goal: create `output/PAPER.md` as the single source of truth for the submitted manuscript text.

Downstream skills (e.g., `claims-extractor`) rely on this file to produce *traceable* claims (section/page/quote pointers).

## Inputs

One of the following (choose the simplest):
- The user pastes the manuscript text in chat (recommended).
- A local PDF provided by the user (extract to text, then paste into `output/PAPER.md`).

Optional context:
- `GOAL.md` (review focus / constraints)

## Outputs

- `output/PAPER.md`

## Procedure

1) Decide the input route
- If `GOAL.md` exists, treat it as the review focus/constraints (but do not rewrite the paper to fit it).
- If the user can paste text: use that.
- If only PDF is available: extract text (any faithful extraction is OK; do not paraphrase).

2) Write `output/PAPER.md`
- Include the full text in order.
- Preserve section headings if possible.
- If page numbers are available, keep simple markers like `\n\n[page 7]\n\n` (helps traceability).

3) Quick sanity check
- Confirm `output/PAPER.md` contains the paper body (not just title/abstract).
- Confirm key sections exist (Intro/Method/Experiments/Conclusion), if the paper has them.

## Acceptance

- `output/PAPER.md` exists.
- The file contains the full manuscript text (or a faithful extraction), sufficient to quote with pointers.

## Troubleshooting

### I only have a PDF and the extracted text is messy

Fix:
- Keep the messy text anyway, but preserve:
  - section headings
  - figure/table captions (if present)
  - page separators (if present)

### The paper is very long

Fix:
- Still ingest the full text.
- If you must split, keep `output/PAPER.md` as the concatenated master file.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/willoscar) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
