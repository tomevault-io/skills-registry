---
name: tex-latex-structure-parser
description: Use when tasks require extracting or validating LaTeX document structure, including sections, citations, figures, tables, cross-references, and custom regex checks.
metadata:
  author: diggerdu
---

# Tex LaTeX Structure Parser

## Overview
Use this skill for read-only structure extraction before any fixing step. Build a factual map of the paper, then hand that evidence to other skills.

## Executable Entry Points
- `scripts/parse_latex_structure.py`: extracts sections, citations, bibliography keys, figures, tables, references, and cross-check issues as JSON.

Example command:
```bash
python scripts/parse_latex_structure.py --project-root . --main-tex main.tex --pretty
```

## Project Adaptation
1. Identify TeX entry file(s) and inclusion style (`\\input`, `\\include`, generated files).
2. Identify bibliography sources (`.bib`, inline bibliography, or mixed).
3. Identify directories to ignore (build, cache, backups, checkpoints).

## Workflow
1. Run `scripts/parse_latex_structure.py` against the target project root.
2. Extract core entities:
   - section hierarchy
   - citation keys used in TeX
   - bibliography keys defined
   - figure/table labels and captions
   - figure/table references (`\\ref`, `\\cref`, equivalents)
3. Run cross-checks from the JSON result:
   - undefined citations
   - uncited bibliography entries
   - unreferenced figures/tables
   - missing labels/captions
4. Apply project-specific regex checks (TODO markers, style constraints, forbidden patterns).
5. Produce a normalized issue list with file and line context.

## Inputs and Outputs
- Input: project root, TeX files, bibliography files, optional regex rules.
- Output: structured facts plus actionable consistency findings.

## Common Mistakes
- Running fixes before building a full structure map.
- Ignoring recursively included TeX files.
- Treating regex matches as final truth without reference cross-checks.
- Mixing parse evidence with subjective style feedback.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/diggerdu) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
