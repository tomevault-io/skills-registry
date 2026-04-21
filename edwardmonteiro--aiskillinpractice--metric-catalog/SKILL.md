---
name: definition-metric-catalog
description: Stakeholders who rely on the metrics. Use when this capability is needed.
metadata:
  author: edwardmonteiro
---

# Purpose
Ensure product and analytics teams align on the metrics that matter, how they are defined, and how they will be reported.

# Pre-run Checklist
- ✅ Review existing dashboards and metric definitions.
- ✅ Confirm segmentation requirements with stakeholders.
- ✅ Verify data availability or instrumentation plans for new metrics.

# Invocation Guidance
```bash
codex run --skill definition.metric_catalog \
  --vars "theme={{theme}}" \
         "required_segments={{required_segments}}" \
         "measurement_tools={{measurement_tools}}" \
         "stakeholders={{stakeholders}}"
```

# Recommended Input Attachments
- Current metric definitions or SQL queries.
- Business reviews or KPI scorecards.

# Claude Workflow Outline
1. Summarize the theme and stakeholders.
2. Build a catalog table with metric names, definitions, formulas, owners, and tools.
3. Detail segmentation requirements, data sources, and known gaps.
4. Provide governance and instrumentation checklist for each metric.
5. Suggest review cadence and communication plan.

# Output Template
```
## Metric Catalog — {{theme}}
| Metric | Definition | Formula / Source | Owner | Tool | Segments |
| --- | --- | --- | --- | --- | --- |

## Segmentation Guidance
- Required Segments:
- Data Availability:
- Known Gaps:

## Governance & Instrumentation
| Metric | Quality Checks | Instrumentation Actions | Review Cadence |
| --- | --- | --- | --- |
```

# Follow-up Actions
- Publish the catalog in the analytics knowledge base.
- Align with engineering on instrumentation stories.
- Schedule periodic metric reviews to ensure definitions stay current.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/edwardmonteiro) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
