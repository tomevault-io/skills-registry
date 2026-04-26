---
name: ai-cost-modeler
description: Use when estimating and tracking AI costs. Use before and during AI projects. Produces cost estimates, TCO models, and ROI analysis.
metadata:
  author: ethical-ai-syndicate
---

# AI Cost Modeler

## Overview

Estimate, track, and optimize AI-related costs across the organization. Model total cost of ownership and build business cases with realistic financial projections.

**Core principle:** AI costs are often underestimated. Comprehensive modeling prevents budget surprises and enables informed decisions.

## When to Use

- Planning new AI projects
- Building business cases
- Budgeting for AI programs
- Optimizing existing AI costs
- Vendor negotiations

## Output Format

```yaml
ai_cost_model:
  project: "[Project name]"
  date: "[YYYY-MM-DD]"
  timeframe: "[Months/Years]"
  
  summary:
    total_year_1: "[$]"
    total_year_3: "[$]"
    monthly_run_rate: "[$]"
    cost_per_transaction: "[$]"
  
  development_costs:
    one_time:
      - category: "Labor"
        description: "Development team"
        calculation: "[FTEs × months × rate]"
        amount: "[$]"
      
      - category: "Infrastructure"
        description: "Initial setup"
        amount: "[$]"
      
      - category: "Data"
        description: "Data preparation, labeling"
        amount: "[$]"
      
      - category: "Vendor"
        description: "Implementation fees"
        amount: "[$]"
    
    total_development: "[$]"
  
  operational_costs:
    recurring:
      - category: "Inference/API"
        description: "[Provider] API calls"
        unit_cost: "[$X per 1K tokens]"
        volume: "[Projected volume]"
        monthly: "[$]"
        annual: "[$]"
      
      - category: "Compute"
        description: "Hosting, training"
        monthly: "[$]"
        annual: "[$]"
      
      - category: "Storage"
        description: "Data, models, logs"
        monthly: "[$]"
        annual: "[$]"
      
      - category: "Labor"
        description: "Ongoing support"
        fte_equivalent: "[X FTEs]"
        annual: "[$]"
      
      - category: "Vendor"
        description: "Subscriptions, licenses"
        annual: "[$]"
    
    total_annual_operational: "[$]"
  
  scaling_projections:
    - year: 1
      volume: "[Usage]"
      cost: "[$]"
    - year: 2
      volume: "[Usage]"
      cost: "[$]"
    - year: 3
      volume: "[Usage]"
      cost: "[$]"
  
  roi_analysis:
    benefits:
      - benefit: "[Benefit description]"
        type: "[Hard savings | Soft savings | Revenue]"
        annual_value: "[$]"
        confidence: "[High | Medium | Low]"
    
    total_annual_benefit: "[$]"
    payback_period: "[Months]"
    year_3_roi: "[%]"
  
  sensitivities:
    - variable: "[What might change]"
      base_assumption: "[Current assumption]"
      impact_if_doubles: "[$ impact]"
  
  recommendations:
    cost_optimization:
      - "[Optimization opportunity]"
```

## Cost Categories

### Inference Costs (LLMs)
```yaml
llm_cost_drivers:
  input_tokens:
    rate: "[$X per 1M tokens]"
    factors:
      - "Prompt length"
      - "Context window usage"
      - "RAG retrieval size"
  
  output_tokens:
    rate: "[$Y per 1M tokens]"
    factors:
      - "Response length requirements"
      - "Streaming vs batch"
  
  volume:
    daily_calls: "[N]"
    avg_tokens_per_call: "[Input + Output]"
    monthly_projection: "[Total tokens]"
  
  example_calculation:
    api: "GPT-4"
    input_rate: "$30/1M"
    output_rate: "$60/1M"
    daily_calls: 10000
    avg_input: 500
    avg_output: 200
    daily_cost: "$15 + $12 = $27"
    monthly_cost: "$810"
    annual_cost: "$9,720"
```

### Compute Costs (ML)
```yaml
compute_cost_drivers:
  training:
    gpu_type: "[A100, H100, etc.]"
    hours_per_run: "[N]"
    runs_per_month: "[N]"
    hourly_rate: "[$]"
    monthly: "[$]"
  
  inference:
    instance_type: "[Type]"
    instances_needed: "[N]"
    hours_per_month: "[N]"
    monthly: "[$]"
  
  development:
    notebooks: "[$]"
    experimentation: "[$]"
```

### Data Costs
```yaml
data_costs:
  storage:
    training_data: "[GB] × [$X/GB]"
    model_artifacts: "[GB] × [$X/GB]"
    logs_monitoring: "[GB] × [$X/GB]"
  
  preparation:
    labeling: "[Hours] × [$/hour]"
    cleaning: "[Effort estimate]"
    enrichment: "[Vendor costs if any]"
  
  acquisition:
    external_data: "[$]"
    api_costs: "[$]"
```

### Labor Costs
```yaml
labor_costs:
  development:
    data_scientist: "[N FTE] × [$/year]"
    ml_engineer: "[N FTE] × [$/year]"
    product_manager: "[N FTE] × [$/year]"
    duration: "[Months]"
  
  operations:
    mlops_engineer: "[N FTE] × [$/year]"
    support: "[N FTE] × [$/year]"
    
  overhead:
    management: "[%]"
    training: "[Hours × rate]"
```

## ROI Calculation

### Benefits Categories
| Type | Examples | How to Quantify |
|------|----------|-----------------|
| **Hard savings** | Labor reduction, error reduction | FTEs × salary, error cost × reduction % |
| **Soft savings** | Time savings, productivity | Hours saved × rate × adoption % |
| **Revenue** | New capability, faster deals | Revenue × attribution % |
| **Risk avoidance** | Compliance, quality | Potential cost × probability reduction |

### ROI Formula
```
ROI = (Total Benefits - Total Costs) / Total Costs × 100

Payback Period = Total Investment / Monthly Net Benefit
```

### Example Calculation
```yaml
roi_example:
  costs:
    development: 200000
    year_1_operations: 100000
    year_1_total: 300000
  
  benefits:
    labor_savings: 400000  # 5 FTEs reassigned
    error_reduction: 50000
    year_1_total: 450000
  
  year_1_net: 150000
  year_1_roi: "50%"
  payback_months: 8
```

## Optimization Strategies

### Inference Optimization
| Strategy | Potential Savings |
|----------|------------------|
| Prompt compression | 20-40% on input tokens |
| Response length limits | Variable on output |
| Caching common queries | 10-30% on volume |
| Model tiering (smaller when possible) | 50-80% on some calls |
| Batch processing | 10-20% vs real-time |

### Compute Optimization
| Strategy | Potential Savings |
|----------|------------------|
| Spot instances | 60-90% on training |
| Right-sizing | 20-50% on inference |
| Auto-scaling | Match cost to demand |
| Reserved capacity | 30-50% on steady state |

## Sensitivity Analysis

```yaml
sensitivity_scenarios:
  base_case:
    volume: 100000
    cost_per_call: 0.01
    monthly: 1000
  
  volume_doubles:
    volume: 200000
    monthly: 2000
    change: "+100%"
  
  price_increases_50:
    cost_per_call: 0.015
    monthly: 1500
    change: "+50%"
  
  both:
    monthly: 3000
    change: "+200%"
```

## Checklist

- [ ] All cost categories identified
- [ ] Volume projections documented
- [ ] Unit costs validated
- [ ] Scaling factors modeled
- [ ] Benefits quantified
- [ ] ROI calculated
- [ ] Sensitivities analyzed
- [ ] Optimization opportunities identified

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ethical-ai-syndicate) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
