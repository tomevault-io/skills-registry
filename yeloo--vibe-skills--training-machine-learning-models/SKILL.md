---
name: training-machine-learning-models
description: | Use when this capability is needed.
metadata:
  author: yeloo
---
# Ml Model Trainer

Use this skill when the user wants to fit a model, choose algorithms, and produce a trained artifact.

## Overview

This skill owns the training loop: selecting candidate models, fitting them with a validation strategy, and producing reusable model outputs.

## When to Use This Skill

- Training a classifier or regressor from prepared data
- Selecting among candidate algorithms and hyperparameters
- Saving a model, pipeline, or reproducible training recipe

## Not For / Boundaries

- Metric selection and benchmark comparison alone: use `evaluating-machine-learning-models`
- Feature creation or transformation strategy alone: use `engineering-features-for-machine-learning`
- Train/test contamination review: use `ml-data-leakage-guard`

## Typical Outputs

- Training plan and candidate model shortlist
- Fitted model or pipeline artifacts
- Notes on validation strategy and handoff to evaluation/reporting

## Related Skills

- `evaluating-machine-learning-models` after fitting
- `ml-data-leakage-guard` before trusting the result

---
> Source: [yeloo/Vibe-Skills](https://github.com/yeloo/Vibe-Skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-22 -->
