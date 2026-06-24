---
name: data-processor
description: Analyze sales data with statistics, regional performance, and commission calculations Use when this capability is needed.
metadata:
  author: acodercat
---

# Data Processor Skill

Use this skill to analyze the stored sales data.

## Injected Variables

After activating this skill, you have access to:

- `sales_data`: List[Dict] of sales transactions with keys: amount, category, region
- `regional_targets`: Dict[str, float] mapping region to sales target

---
> Source: [acodercat/cave-agent](https://github.com/acodercat/cave-agent) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-24 -->
