---
name: preprocessing-data-with-automated-pipelines
description: | Use when this capability is needed.
metadata:
  author: foryourhealth111-pixel
---
# Data Preprocessing Pipeline

## Positioning

Use this skill as the direct owner for ML input-preparation pipelines.

It covers preprocessing-heavy tasks where the requested deliverable is a repeatable pipeline for cleaning, encoding, transforming, and validating input data.

## When to Use

Use this skill when:
- Prepare raw data for machine learning models.
- Automate data cleaning and transformation processes.
- Implement a robust ETL (Extract, Transform, Load) pipeline.

## Not For / Boundaries

- Whole-task ML ownership: use `scikit-learn` or `ml-pipeline-workflow`
- Leakage and prediction-time auditing: use `ml-data-leakage-guard`
- Grouped scientific preprocessing with stronger methodological constraints: use `scientific-data-preprocessing`

## Typical Outputs

- A preprocessing pipeline plan or implementation sketch
- Clear sequencing for clean, encode, transform, and validate steps
- Notes that identify where leakage review, training, or evaluation should be run next

## Related Skills

- `ml-data-leakage-guard` before trusting fitted preprocessing steps
- `splitting-datasets` when the next narrow problem is partition strategy

---
> Source: [foryourhealth111-pixel/Vibe-Skills](https://github.com/foryourhealth111-pixel/Vibe-Skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-23 -->
