---
name: model-lifecycle-manager
description: Use when managing AI/ML models in production. Use after model deployed. Produces model registry entries, drift detection schedules, retraining triggers, and version management workflows.
metadata:
  author: ethical-ai-syndicate
---

# Model Lifecycle Manager

## Overview

Manage the full lifecycle of AI/ML models from deployment through retirement. Track versions, monitor for drift, schedule retraining, and maintain clear lineage.

**Core principle:** Production models require active management. Deploy and forget leads to degraded performance and silent failures.

## When to Use

- New model deployed to production
- Setting up monitoring for existing models
- Planning retraining cadence
- Investigating model performance issues
- Preparing for model retirement

## Output Format

```yaml
model_lifecycle:
  model_id: "[Unique identifier]"
  model_name: "[Human-readable name]"
  version: "[Current version]"
  
  metadata:
    purpose: "[What the model does]"
    owner: "[Team/person responsible]"
    deployed_date: "[YYYY-MM-DD]"
    framework: "[TensorFlow | PyTorch | Scikit-learn | etc.]"
    model_type: "[Classification | Regression | LLM | etc.]"
    training_data_snapshot: "[Reference to training data version]"
  
  deployment:
    environment: "[Production | Staging | etc.]"
    endpoint: "[API endpoint or service]"
    infrastructure: "[Cloud service, instance type]"
    scaling_config:
      min_instances: "[N]"
      max_instances: "[N]"
      auto_scale_metric: "[What triggers scaling]"
  
  monitoring:
    performance_metrics:
      - metric: "[Accuracy | Precision | Latency | etc.]"
        current: "[Value]"
        threshold: "[Alert if below/above]"
        measurement_frequency: "[Real-time | Hourly | Daily]"
    
    drift_detection:
      data_drift:
        method: "[PSI | KS-test | etc.]"
        features_monitored: ["[Feature 1]", "[Feature 2]"]
        threshold: "[Trigger value]"
        check_frequency: "[Daily | Weekly]"
      
      concept_drift:
        method: "[Performance degradation | Label drift]"
        window: "[Time period]"
        threshold: "[% change to trigger]"
    
    alerts:
      - condition: "[What triggers alert]"
        severity: "[Critical | Warning | Info]"
        channel: "[Slack | PagerDuty | Email]"
        runbook: "[Link to response procedure]"
  
  retraining:
    trigger_conditions:
      - trigger: "[Drift threshold exceeded]"
        action: "Initiate retraining pipeline"
      - trigger: "[Scheduled: Monthly]"
        action: "Evaluate if retraining needed"
    
    pipeline:
      location: "[CI/CD pipeline reference]"
      data_sources: ["[Training data locations]"]
      validation_requirements:
        - "[Must meet baseline accuracy]"
        - "[A/B test against current model]"
    
    approval_process:
      auto_deploy_if: "[Conditions for automatic deployment]"
      manual_review_if: "[Conditions requiring human review]"
      approvers: ["[Role/person]"]
  
  versioning:
    current_version: "[X.Y.Z]"
    previous_versions:
      - version: "[X.Y.Z-1]"
        deployed: "[Date range]"
        retired_reason: "[Why replaced]"
    
    rollback:
      available_versions: ["[List]"]
      rollback_procedure: "[Steps]"
      rollback_time: "[Expected duration]"
  
  retirement:
    status: "[Active | Deprecated | Retired]"
    deprecation_date: "[If applicable]"
    replacement: "[Successor model if any]"
    data_retention: "[How long to keep artifacts]"
```

## Lifecycle Stages

```
┌─────────────┐    ┌─────────────┐    ┌─────────────┐
│ Development │───>│  Staging    │───>│ Production  │
└─────────────┘    └─────────────┘    └─────────────┘
                                            │
        ┌───────────────────────────────────┤
        │                                   │
        ▼                                   ▼
┌─────────────┐                     ┌─────────────┐
│  Retrain    │<────────────────────│  Monitor    │
└─────────────┘    (drift detected) └─────────────┘
        │
        ▼
┌─────────────┐
│  Validate   │───> (pass) ───> Promote to Production
└─────────────┘
        │
        └───> (fail) ───> Investigate, iterate
```

## Drift Detection

### Data Drift
Input feature distributions change:

| Method | When to Use | Threshold |
|--------|-------------|-----------|
| PSI (Population Stability Index) | Categorical features | >0.2 significant |
| KS Test | Continuous features | p < 0.05 |
| Chi-Square | Categorical distributions | p < 0.05 |

### Concept Drift
Relationship between inputs and outputs changes:

| Indicator | Detection Method |
|-----------|-----------------|
| Accuracy degradation | Compare to baseline over rolling window |
| Prediction distribution shift | Monitor output distribution |
| Ground truth lag | Compare delayed labels to predictions |

## Retraining Triggers

| Trigger Type | Condition | Response |
|--------------|-----------|----------|
| **Scheduled** | Time-based (monthly/quarterly) | Evaluate metrics, retrain if needed |
| **Performance** | Accuracy drops below threshold | Initiate retraining pipeline |
| **Data drift** | Feature distributions shift | Evaluate impact, retrain if significant |
| **Manual** | Business request or known issue | Ad-hoc retraining |

## Version Control

### Semantic Versioning for Models
```
v[MAJOR].[MINOR].[PATCH]

MAJOR: Architecture change, breaking API change
MINOR: Retraining with new data, feature additions
PATCH: Bug fixes, hyperparameter tuning
```

### Artifacts to Version
- Model weights/files
- Training configuration
- Feature engineering code
- Training data snapshot reference
- Evaluation metrics

## Monitoring Dashboard

```yaml
dashboard_components:
  health_summary:
    - "Model status (healthy/degraded/down)"
    - "Last prediction timestamp"
    - "Error rate (last 24h)"
  
  performance:
    - "Primary metric trend (30 days)"
    - "Latency p50/p95/p99"
    - "Throughput (predictions/sec)"
  
  drift:
    - "Data drift score by feature"
    - "Prediction distribution"
    - "Drift trend over time"
  
  operations:
    - "Resource utilization"
    - "Cost per prediction"
    - "Version history"
```

## Checklist

- [ ] Model registered with unique ID
- [ ] Metadata complete (owner, purpose, framework)
- [ ] Monitoring configured with alerts
- [ ] Drift detection enabled
- [ ] Retraining pipeline tested
- [ ] Rollback procedure documented
- [ ] Retirement plan defined

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ethical-ai-syndicate) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
