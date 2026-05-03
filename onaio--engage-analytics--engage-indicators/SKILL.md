---
name: engage-indicators
description: Manage indicators and metrics in the Engage Analytics dbt project. Use when working with healthcare analytics indicators, adding new metrics, creating questionnaire response models, or understanding the metrics architecture. Triggers on requests involving indicator creation, metric definitions, questionnaire data models, PHQ-9/GAD-7 scores, mwTool eligibility, or dbt model development for Engage. Use when this capability is needed.
metadata:
  author: onaio
---

# Engage Indicators

Manage indicators and metrics in the Engage Analytics dbt project at `/Volumes/Biliba/github/engage-analytics/dbt`.

## Quick Reference

- **Indicator specs**: `indicators/engage-indicators.csv`
- **Metrics catalog**: `macros/metrics.sql`
- **Metrics fact table**: `engage_analytics.fct_metrics_long`
- **Project docs**: `docs/metrics.md`

**Scripts** (in repo root):
- `model_generator.py` - Generate questionnaire models (named + anon)
- `metadata_manager.py` - Manage questionnaire metadata
- `run_dbt.sh` - Run dbt commands with env vars

For full project structure, see [references/project-structure.md](references/project-structure.md).
For indicator-to-metric mapping, see [references/indicator-mapping.md](references/indicator-mapping.md).

## Core Capabilities

### 1. Understand Existing Indicators

Query current metrics:
```sql
SELECT metric_id, description, max(value) as latest_value
FROM engage_analytics.fct_metrics_long
GROUP BY 1, 2 ORDER BY 1;
```

Check indicator coverage in `docs/metrics.md` - maps all 32 CSV indicators to 64 dbt metrics.

### 2. Create New Indicator

**Guided workflow - ask user these questions:**

1. **What is the indicator name and description?**
2. **What domain?** (System Use, Programmatic, Treatment, Follow-up, Adoption)
3. **What module?** (mwTool, IPC, SBIRT, SPI, FWS, Planning Next Steps, All)
4. **What is the data source?**
   - Questionnaire ID (e.g., `Questionnaire/1613532`)
   - Task code (e.g., `040` for follow-up)
   - Existing model (e.g., `patient`, `practitioners`)
5. **What linkId or field contains the data?** (for questionnaire-based)
6. **What is the data type?** (Count, Percent)
7. **For percent metrics: what is the numerator and denominator?**
8. **What disaggregation?** (by organization, by practitioner, by month)

**Implementation steps:**

A. **If new source model needed**, create in `models/metrics/`:
```sql
-- models/metrics/new_metric_source.sql
-- ABOUTME: [What this model does]
-- ABOUTME: [What indicator it supports]

{{ config(materialized='view') }}

select
    subject_patient_id,
    organization_id,
    -- metric-specific fields
from {{ ref('source_model') }}
where conditions
```

B. **Add to metrics catalog** in `macros/metrics.sql`:
```yaml
- id: new_metric_name
  unit: count  # or percent
  grain: day
  entity_keys: [organization_id]
  source_model: new_metric_source
  expression: "count(distinct subject_patient_id)"  # for count
  # OR for percent:
  # numerator: "count(distinct case when condition then subject_patient_id end)"
  # denominator: "nullif(count(distinct subject_patient_id), 0)"
  description: "Human-readable description"
  version: v1
```

C. **Rebuild metrics**:
```bash
cd /Volumes/Biliba/github/engage-analytics/dbt
uv run dbt run --profiles-dir . --select new_metric_source fct_metrics_long
```

D. **Verify**:
```sql
SELECT * FROM engage_analytics.fct_metrics_long
WHERE metric_id = 'new_metric_name';
```

### 3. Add New Questionnaire

When a new form is added to the app:

A. **Find questionnaire ID** in raw data:
```sql
SELECT DISTINCT questionnaire_id
FROM engage_analytics_engage_analytics_stg.stg_questionnaire_response
ORDER BY 1;
```

B. **Extract metadata** using `metadata_manager.py`:
```bash
cd /Volumes/Biliba/github/engage-analytics/dbt
python3 metadata_manager.py extract --questionnaire-id NEW_ID
```
This extracts linkIds and adds them to `data/questionnaire_metadata.csv`.

C. **Review and edit metadata** in `data/questionnaire_metadata.csv`:
- Set `anon=TRUE` for PII fields (names, DOB, phone, address)
- Set `anon=FALSE` for non-PII fields
- Add readable `label` for each field

D. **Generate models** using `model_generator.py`:
```bash
# Generate both named and anonymized models
python3 model_generator.py all --table qr_new_form --questionnaire-id NEW_ID

# Or generate separately:
python3 model_generator.py named --table qr_new_form --questionnaire-id NEW_ID
python3 model_generator.py anon --table qr_new_form
```

E. **Build**:
```bash
uv run dbt seed --profiles-dir .  # Reload metadata
uv run dbt run --profiles-dir . --select qr_new_form qr_new_form_anon
```

**Manual model creation** (if scripts unavailable):

Named model (`models/marts/qr_named/qr_new_form.sql`):
```sql
{{ config(materialized='view') }}
{% set identifiers = ["Questionnaire/NEW_ID"] %}
{% if identifiers|length == 0 %}
  select null::text as placeholder where false
{% else %}
  {{ build_qr_wide_readable(identifiers, this.name) }}
{% endif %}
```

Anonymized model (`models/marts/qr_anon/qr_new_form_anon.sql`):
```sql
{{ config(materialized='view') }}
{{ create_anonymized_qr_view('qr_new_form', []) }}
```

### 4. Update Anonymization

Edit `data/questionnaire_metadata.csv`:
- Set `anon=TRUE` for PII fields (names, DOB, phone, address, SSN, Medicaid)
- Set `anon=FALSE` for non-PII fields

Rebuild anonymized view:
```bash
uv run dbt seed --profiles-dir .
uv run dbt run --profiles-dir . --select qr_*_anon
```

### 5. Test Metrics

Verify metric logic matches source data:
```sql
-- Get metric value
SELECT metric_id, organization_id, value
FROM engage_analytics.fct_metrics_long
WHERE metric_id = 'metric_name';

-- Verify against source
SELECT organization_id, count(distinct subject_patient_id)
FROM engage_analytics.source_model
GROUP BY 1;
```

## Common Patterns

### Eligibility from mwTool
```sql
-- Extract boolean from mwTool (Questionnaire/1613532)
(jsonb_path_query_first(items::jsonb,
  '$.**.item[*] ? (@.linkId == "flag-name").answer[0].valueBoolean'))::boolean
```

### Acceptance from Planning Next Steps
```sql
-- Check acceptance field
WHERE planning_next_steps_did_the_client_accept_X = 'true'
```

### Session Tracking
```sql
-- Count sessions by intervention type
SELECT intervention_type, count(distinct qr_id)
FROM engage_analytics.intervention_sessions
GROUP BY 1;
```

### Assessment Score Severity
- PHQ-9: Severe 20-27, Moderate 10-19, Mild 5-9, Minimal 0-4
- GAD-7: Severe 15-21, Moderate 10-14, Mild 5-9, Minimal 0-4

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/onaio) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
