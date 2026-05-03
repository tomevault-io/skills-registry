---
name: paper-extract-style
description: Extract academic style, build domain knowledge base (lexicon/domains), and generate style guide from PDF/TeX files. Use when this capability is needed.
metadata:
  author: xiaooomd
---

# Paper Style Extraction Skill

**Goal**: Reverse engineer academic style from PDF/TeX files and build the `psmfiles/` knowledge base.

## Workflow

1.  **Locate Source Files**:
    *   Look for PDF, TeX, or TXT files in the current directory or user-specified path.
    *   If user requests new papers/datasets, use `WebSearch` to find them first.

2.  **Extract Text**:
    *   Execute the extraction script: `python paper-extract-style/extract_text.py <file_path>`
    *   This creates `ref_article/*_cleaned_body.md` and `ref_article/*_appendix.md`.

3.  **Build Knowledge Base (Incremental Fusion)**:
    *   **Read**: Process `ref_article/*_cleaned_body.md`.
    *   **Update `psmfiles/lexicon_domain.md`**:
        *   Extract high-frequency sentence patterns for each section (Abstract, Intro, Methods, Experiments, Conclusion).
        *   **Generalize**: Replace specific nouns with placeholders (e.g., `[Method]`, `[Metric]`).
        *   **De-duplicate**: Do not copy the static skeleton from `LEXICON.md`.
    *   **Update `psmfiles/DOMAINS_Knowledge.md`**:
        *   Extract "Territory" statements (Industrial impact).
        *   Extract "Niche" statements (Critiques of prior work, Solved problems).
        *   Extract "Trends" and "Citations".
        *   **Fusion Logic**: Append new insights; merge duplicate views to reinforce arguments. Do NOT overwrite existing data.
    *   **Generate `psmfiles/STYLE_GUIDE.md`**:
        *   Read `paper-extract-style/TEMPLATES.md`.
        *   Fill in the template based on observed style (Voice, Narrative Flow, Formatting).
        *   **Statistics**: Calculate average sentence length for each section.
        *   **Appendix Check**: Check `*_appendix.md` to define Appendix formatting standards.

4.  **Completion**:
    *   Report the location of generated assets in `psmfiles/`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/xiaooomd) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
