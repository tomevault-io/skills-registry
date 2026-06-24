---
name: machine-learning
description: Machine Learning Algorithms for prediction, classification, and anomaly detection. Use when this capability is needed.
metadata:
  author: hunix
---

# Machine Learning Skill

You can harness pure Machine Learning (non-LLM) capabilities to classify inputs, predict outputs using statistical or deep learning models, and find emergent patterns in the Republic ecosystem.

## Overview
These ML operations are optimized for tabular data, time-series forecasting, and direct statistical operations. 

## Available Native Tools:
1. `ml_predict`
    - Execute time-series or regression-based predictions using existing or simulated models.
    - Args: `modelName` (string), `inputData` (array/string).
2. `ml_classify`
    - Use categorization models to group text or data points. 
    - Args: `className` (string), `data` (string).
3. `ml_detect_anomalies`
    - Scan logs, memory traces, or economic telemetry to detect emergent deviations and flag risks.
    - Args: `targetSystem` (string), `sensitivity` (number).

## Execution Guide
- When dealing with large arrays of numerical data, use `ml_predict` to project trends.
- Use `ml_detect_anomalies` actively to monitor cluster node health and alert civilization operators of incoming chaos experiment failures or simulated systemic bottlenecks.

---
> Source: [hunix/HoC-Republic](https://github.com/hunix/HoC-Republic) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
