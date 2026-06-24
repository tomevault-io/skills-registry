---
name: alicloud-data-lake-dlf-next-test
description: Minimal smoke test for DlfNext skill. Validate metadata discovery and one read-only API call. Use when this capability is needed.
metadata:
  author: cinience
---

Category: test

# DlfNext Minimal Viable Test

## Prerequisites

- AK/SK and region are configured.
- GoalsSkill: `skills/data-lake/alicloud-data-lake-dlf-next/`。

## Test Steps

1) Run `python scripts/list_openapi_meta_apis.py`.
2) Select one read-only API and run a minimal request.
3) Save outputs under `output/alicloud-data-lake-dlf-next-test/`。

## Expected Results

- Metadata and read-only query paths are available.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/cinience) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
