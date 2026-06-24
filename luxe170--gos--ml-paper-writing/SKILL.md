---
name: ml-paper-writing
description: Write machine learning papers with domain-specific conventions: LaTeX templates, figure generation, experiment tables, and reproducibility checklists. Use when this capability is needed.
metadata:
  author: luxe170
---
# ML Paper Writing

Domain-specific paper writing for machine learning venues.

## ML Paper Conventions

- **Abstract** — Task, approach, key result in 200 words
- **Method** — Architecture diagrams, loss functions, training details
- **Experiments** — Datasets, baselines, ablation studies, error bars
- **Results** — Tables with SOTA comparison, significance tests
- **Reproducibility** — Code, hyperparameters, random seeds, hardware specs

## Supported Venues

ICLR, NeurIPS, ICML, CVPR, ACL, EMNLP, AAAI, IJCAI, and major workshops.

## LaTeX Automation

Generates properly formatted tables from experiment logs,
creates architecture diagrams from model configs, and ensures
venue-specific template compliance.

## Dependencies

- Takes experiment results as structured input (JSON/YAML)
- Uses `scientific-writing` for language polish
- Works with `inno-reference-audit` for citation verification

---
> Source: [luxe170/GOS](https://github.com/luxe170/GOS) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
