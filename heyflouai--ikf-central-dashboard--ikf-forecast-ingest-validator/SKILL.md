---
name: ikf-forecast-ingest-validator
description: Validates ingest/mapping for both column-based and heatmap-based forecast files (files_examples) into canonical DB rows and Heatmap Contract output.
metadata:
  author: heyflouai
---

# IKF Forecast Ingest Validator

## Objective
Support BOTH forecast formats and validate that canonical output renders correctly in heatmaps.

## Inputs
- files_examples/s&p500_stocks_flat (column-based)
- files_examples/etfs (heatmap-based, two universes)

## Requirements
- Parse signal as real number (not %).
- Parse pred and display as 0.xx.
- Map horizons correctly (3D, 7D, 14D, 1M, 3M, 1Y).
- Ensure canonical DB rows can render every existing UI surface accurately.

## Output
- Mapping spec for each file type.
- Validation checklist to compare “expected values” vs “rendered values.”
- Edge cases to test (missing cells, malformed values, unknown tickers).

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/heyflouai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
