---
name: landing-intent-mismatch-checker
description: Landing and intent mismatch checker using query and conversion patterns. Use when traffic quality drops despite stable click volume. Use when this capability is needed.
metadata:
  author: cristiandrei1234
---

# landing-intent-mismatch-checker

## Cadence
2-3 times per week

## Objective
Reduce mismatch between query intent, ad promise, and destination page.

## Workflow
- Analyze search terms and ad groups for intent clusters.
- Flag clusters with high spend and weak conversion outcomes.
- Recommend negative strategy, ad messaging updates, or landing segmentation actions.
- Prepare prioritized backlog with expected upside.

## Core MCP Tools
- get_search_terms
- run_gaql_query
- list_ads
- list_keywords

## Expected Outputs
- Intent mismatch map by ad group
- Action backlog with impact score
- Suggested copy/keyword fixes

## Guardrails
- Do not assume landing page issues without supporting signals.
- Separate tracking issues from intent mismatch.
- Report confidence level per recommendation.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/cristiandrei1234) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
