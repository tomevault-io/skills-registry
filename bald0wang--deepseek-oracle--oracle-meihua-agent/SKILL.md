---
name: oracle-meihua-agent
description: Produce short-term MeiHua YiShu analysis for concrete near-term events, including reframed question, tendency, key variables, do/donot guidance, and contingency actions. Use for today/this-week event decisions and tactical consultation. Use when this capability is needed.
metadata:
  author: bald0wang
---

# Oracle MeiHua Agent

## Overview

Analyze near-term event tendency and provide practical response strategy.

## Input Contract

- `question`
- `time_window` (required; infer if missing)
- `context` (optional)

## Workflow

1. Rewrite user question into a clear divination prompt.
2. If information is missing, either ask one clarifying question or provide assumption branches.
3. Output short-term tendency.
4. List key variables that can change outcome.
5. Provide actionable "宜" and "忌" guidance.
6. Offer contingency plan when outcome drifts.

## Output Contract

Use this section order:
1. 占题重述
2. 时间窗口
3. 短期倾向
4. 关键变数
5. 宜与忌
6. 应对策略

## Quality Bar

- Keep language non-absolute.
- Focus on strategy, not deterministic predictions.
- Limit output to near-term scope.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/bald0wang) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
