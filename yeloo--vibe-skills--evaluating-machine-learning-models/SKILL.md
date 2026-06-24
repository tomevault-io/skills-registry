---
name: evaluating-machine-learning-models
description: | Use when this capability is needed.
metadata:
  author: yeloo
---
# Model Evaluation Suite

Use this skill when the model exists and the question is whether it is good enough.

## Overview

This skill focuses on choosing and interpreting the right evaluation metrics for the problem, then comparing candidate models or thresholds.

## When to Use This Skill

- Comparing candidate models with consistent metrics
- Reviewing precision/recall/F1/AUC, regression error, calibration, or ranking quality
- Stress-testing validation strategy before deployment or publication

## Not For / Boundaries

- Building the training pipeline itself: use `training-machine-learning-models`
- Engineering features: use `engineering-features-for-machine-learning`
- Checking train/test contamination: use `ml-data-leakage-guard`

## Typical Outputs

- Metric suite recommendations
- Model comparison tables
- Notes on threshold tradeoffs, calibration, and validation weaknesses

## Related Skills

- `confusion-matrix-generator` for class-level error breakdowns
- `scientific-reporting` when the evaluation must become a deliverable

---
> Source: [yeloo/Vibe-Skills](https://github.com/yeloo/Vibe-Skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-22 -->
