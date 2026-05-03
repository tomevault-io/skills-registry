---
name: pdf-docx-paper-summary-llm
description: LLM-enforced PDF to DOCX paper summaries: read each PDF, write Chinese review-style sections (研究背景/研究内容/主要结论) with anti-repetition QA, extract figures/tables, and render into a provided template docx (e.g., 示例.docx). Use for batch-processing academic PDFs into high-quality, logic-driven Word summaries. Use when this capability is needed.
metadata:
  author: xyf1qgnn-cpu
---

# PDF -> DOCX Paper Summary (LLM-Enforced)

## Overview

Generate per-paper Word summaries that match a provided template `.docx`, while **forcing** the agent to: (1) read the PDF, (2) write a logical Chinese review (not sentence-by-sentence translation), and (3) pass QA before producing the final docx.

## Workflow (per PDF)

Inputs:
- Template docx (e.g. `示例.docx`)
- Paper PDF (e.g. `pdfs/<paper>.pdf`)
- Output folder (recommended: `out/<paper-id>/`)

Steps:

1) Extract a “paper pack” (text excerpts + figures/tables + key facts)

```bash
python "${CODEX_HOME:-$HOME/.codex}/skills/pdf-docx-paper-summary-llm/scripts/extract_paper_pack.py" \
  --pdf 'pdfs/<paper>.pdf' \
  --assets-dir 'out/<paper-id>/assets' \
  --out-json 'out/<paper-id>/paper_pack.json'
```

2) Read the PDF (LLM step) and write the config JSON

Create `docs/plans/<paper-id>.json` following:
- `references/config-format.md`
- `references/writing-standard.md` (this is the “示例.docx” writing standard)

Hard rules (do not violate):
- No “原文要点改写 / 补充讨论 / 逐句翻译堆段落”
- Each section must be **logical** and **paper-specific** (models/data/metrics/codes/limits)
- Paragraphs must not devolve into one-sentence repetition

3) Run QA on the JSON (must pass before building docx)

```bash
python "${CODEX_HOME:-$HOME/.codex}/skills/pdf-docx-paper-summary-llm/scripts/qa_config.py" \
  --config 'docs/plans/<paper-id>.json' \
  --pack 'out/<paper-id>/paper_pack.json'
```

If QA fails, revise the JSON (do not “paper over” errors with generic filler).

4) Build the final docx (template-preserving)

```bash
python "${CODEX_HOME:-$HOME/.codex}/skills/pdf-docx-paper-summary-llm/scripts/build_paper_docx.py" \
  --template '示例.docx' \
  --config 'docs/plans/<paper-id>.json' \
  --pdf 'pdfs/<paper>.pdf' \
  --out-docx 'out/<paper-id>/<paper-id>_summary.docx' \
  --assets-dir 'out/<paper-id>/assets'
```

## Workflow (batch)

Process a directory of PDFs by looping the per-PDF workflow. In batch mode, do not move on to the next paper until the current paper passes `qa_config.py`.

Recommended output layout:

```
docs/plans/<paper-id>.json
out/<paper-id>/paper_pack.json
out/<paper-id>/assets/FigXX.png
out/<paper-id>/<paper-id>_summary.docx
```

## Notes

- This skill **intentionally** refuses generic padding. If you cannot extract enough paper-specific details, go back to the PDF and read deeper (Methods/Results).
- Keep images supportive, not dominant (QA also checks text quality).

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/xyf1qgnn-cpu) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
