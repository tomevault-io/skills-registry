---
name: engineering-features-for-machine-learning
description: | Use when this capability is needed.
metadata:
  author: yeloo
---
# Feature Engineering Toolkit

Use this skill when the main question is how to improve or restructure the input features.

## Overview

This skill covers feature creation, encoding, scaling coordination, and feature selection before the model is finalized.

## When to Use This Skill

- Creating derived variables, interaction terms, bins, encodings, or date-based features
- Selecting or pruning features before training
- Reworking feature representations to fit model assumptions or data geometry

## Not For / Boundaries

- Full training runs and benchmark ownership: use `training-machine-learning-models`
- Post-hoc interpretation of a trained model: use `feature-importance-analyzer`
- Leak checking across the preprocessing order: use `ml-data-leakage-guard`

## Typical Outputs

- Candidate feature set changes
- Implementation notes for encoders, scalers, and selectors
- Rationale for what to keep, drop, or combine

## Related Skills

- `data-normalization-tool` for scaling-only questions
- `ml-data-leakage-guard` before accepting the engineered pipeline

---
> Source: [yeloo/Vibe-Skills](https://github.com/yeloo/Vibe-Skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-22 -->
