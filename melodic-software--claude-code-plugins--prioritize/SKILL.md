---
name: prioritize
description: Apply prioritization methods to elicited requirements. Supports MoSCoW, Kano Model, WSJF, and Wiegers' scoring. Outputs ranked requirements with justification. Use when this capability is needed.
metadata:
  author: melodic-software
---

# Prioritize Command

Apply prioritization methods to rank requirements by business value, customer impact, or implementation efficiency.

## Usage

```bash
/requirements-elicitation:prioritize
/requirements-elicitation:prioritize --domain "checkout"
/requirements-elicitation:prioritize --domain "auth" --method wsjf
/requirements-elicitation:prioritize --domain "onboarding" --method kano --interactive
```

## Arguments

| Argument | Required | Description |
|----------|----------|-------------|
| --domain | No | Domain to prioritize (default: current/most recent) |
| --method | No | Prioritization method: `moscow`, `kano`, `wsjf`, `wiegers`, `opportunity` (default: `moscow`) |
| --interactive | No | Enable interactive scoring mode for stakeholder input |

## Workflow

### Step 1: Load Requirements

Read synthesized requirements from `.requirements/{domain}/synthesis/`.

### Step 2: Select Method

Choose prioritization method based on context:

```yaml
method_selection:
  moscow:
    best_for: "Quick categorization, MVP scope definition"
    requires: "Basic stakeholder consensus on importance"
    output: "Must/Should/Could/Won't categories"

  kano:
    best_for: "Customer satisfaction analysis"
    requires: "Customer survey data or simulation"
    output: "Basic/Performance/Excitement classification"

  wsjf:
    best_for: "Agile/SAFe environments, flow optimization"
    requires: "Cost of Delay and Job Size estimates"
    output: "Numeric priority scores for backlog ordering"

  wiegers:
    best_for: "Quantitative value/cost/risk analysis"
    requires: "Multi-stakeholder scoring input"
    output: "Weighted priority scores with rationale"

  opportunity:
    best_for: "JTBD-aligned prioritization"
    requires: "Importance and satisfaction ratings"
    output: "Opportunity scores highlighting underserved needs"
```

### Step 3: Score Requirements

#### MoSCoW Scoring

```yaml
moscow_scoring:
  process:
    1. "Start with MUST - be strict (if everything is MUST, nothing is)"
    2. "Move to WON'T - explicitly exclude"
    3. "Distribute remaining between SHOULD and COULD"
    4. "Validate: MUSTs should be ~60% of capacity"

  categories:
    must:
      criteria:
        - "System won't work without it"
        - "Legal/regulatory requirement"
        - "Core to value proposition"

    should:
      criteria:
        - "Significant value, but workarounds exist"
        - "Key stakeholder expectations"

    could:
      criteria:
        - "Enhances user experience"
        - "Low effort, incremental value"

    wont:
      criteria:
        - "Agreed to defer, not rejected"
        - "Future consideration"
```

#### WSJF Scoring

```yaml
wsjf_scoring:
  formula: "WSJF = Cost of Delay / Job Size"

  cost_of_delay:
    user_value: "1-10 (Value to end users)"
    time_criticality: "1-10 (How much value decays with time)"
    risk_reduction: "1-10 (Risk/opportunity enabled)"

  job_size: "Fibonacci (1, 2, 3, 5, 8, 13)"

  interpretation:
    "WSJF > 3": "High priority - do first"
    "WSJF 1-3": "Medium priority"
    "WSJF < 1": "Low priority - consider deferring"
```

#### Kano Scoring

```yaml
kano_scoring:
  question_pair:
    functional: "How would you feel if [feature] was present?"
    dysfunctional: "How would you feel if [feature] was absent?"

  answers:
    - "I would like it"
    - "I expect it"
    - "I'm neutral"
    - "I can tolerate it"
    - "I dislike it"

  classification:
    "Like + Dislike": "Excitement (Delighter)"
    "Expect + Dislike": "Basic (Must-Be)"
    "Like + Neutral": "Performance"
    "Neutral + Neutral": "Indifferent"
```

#### Wiegers' Scoring

```yaml
wiegers_scoring:
  dimensions:
    value: "1-9 (Customer/business value)"
    penalty: "1-9 (Penalty for NOT having it)"
    cost: "1-9 (Implementation effort)"
    risk: "1-9 (Technical uncertainty)"

  formula: |
    Priority = (Value × 2 + Penalty × 1) /
               (Cost × 1 + Risk × 0.5)

  interpretation:
    "> 3.0": "High priority"
    "2.0-3.0": "Medium priority"
    "< 2.0": "Low priority"
```

#### Opportunity Scoring

```yaml
opportunity_scoring:
  formula: "Opportunity = Importance + (Importance - Satisfaction)"

  data_collection:
    importance: "How important is [outcome]? (1-10)"
    satisfaction: "How satisfied with current ability? (1-10)"

  interpretation:
    "15-20": "High opportunity - underserved"
    "10-15": "Moderate opportunity"
    "5-10": "Low opportunity - well served"
    "< 5": "Over-served - deprioritize"
```

### Step 4: Interactive Mode (Optional)

When `--interactive` is specified:

```yaml
interactive_flow:
  1. Display each requirement one at a time
  2. Prompt for scores based on selected method
  3. Allow notes and rationale capture
  4. Support skip and return later
  5. Show running totals and distributions
```

### Step 5: Generate Output

Create prioritized requirements list with:

```yaml
priority_report:
  domain: "{domain}"
  method: "{method used}"
  date: "{ISO-8601}"
  total_requirements: 25

  prioritized_list:
    - rank: 1
      requirement_id: "REQ-001"
      title: "Two-Factor Authentication"
      category: "must"  # or score for numeric methods
      score: 3.54  # if applicable
      rationale: "High penalty for omission, compliance requirement"

    - rank: 2
      requirement_id: "REQ-015"
      title: "Mobile Offline Mode"
      category: "should"
      score: 3.2
      rationale: "High user value, competitive pressure"

  summary:
    by_category:  # For MoSCoW/Kano
      must: 8
      should: 10
      could: 5
      wont: 2

    recommendations:
      - "Focus on top 5 high-priority items immediately"
      - "Consider deferring low-priority items with high cost"
      - "Re-evaluate in 2 weeks based on feedback"
```

## Output Formats

### Default: YAML Summary

```yaml
# .requirements/{domain}/prioritization/PRIO-{timestamp}.yaml
prioritization:
  method: "wsjf"
  date: "2025-12-26T15:00:00Z"
  requirements:
    - id: "REQ-001"
      title: "Feature Name"
      score: 3.54
      rank: 1
      rationale: "..."
```

### Markdown Table

```markdown
| Rank | ID | Title | Score | Category | Rationale |
|------|-----|-------|-------|----------|-----------|
| 1 | REQ-001 | Two-Factor Auth | 3.54 | Must | Compliance |
| 2 | REQ-015 | Offline Mode | 3.2 | Should | User value |
```

## Example Session

```text
/requirements-elicitation:prioritize --domain "e-commerce" --method wsjf

Loading requirements from .requirements/e-commerce/synthesis/...
Found 28 requirements to prioritize.

Applying WSJF scoring...

Scoring REQ-001: "Product Search"
  User Value: 9 (highly requested)
  Time Criticality: 7 (competitors have this)
  Risk Reduction: 5 (reduces support load)
  Job Size: 5 (moderate complexity)
  → WSJF Score: 4.2 (High Priority)

Scoring REQ-002: "Wishlist Feature"
  User Value: 6 (nice to have)
  Time Criticality: 2 (not urgent)
  Risk Reduction: 2 (minimal risk impact)
  Job Size: 3 (low complexity)
  → WSJF Score: 3.3 (Medium Priority)

[... continues for all requirements ...]

═══════════════════════════════════════════════
PRIORITIZATION COMPLETE
═══════════════════════════════════════════════

Top 5 Priorities (WSJF):
1. REQ-001 Product Search (4.2)
2. REQ-008 Checkout Flow (3.9)
3. REQ-012 Order Tracking (3.7)
4. REQ-002 Wishlist (3.3)
5. REQ-015 Reviews (3.1)

Distribution:
  High (>3.0): 12 requirements
  Medium (1-3): 10 requirements
  Low (<1.0): 6 requirements

Saved to: .requirements/e-commerce/prioritization/PRIO-20251226-150000.yaml

Recommendations:
• Sprint 1: Focus on top 5 (Product Search, Checkout, Order Tracking)
• Defer: 6 low-priority items until MVP validated
• Re-evaluate: Wishlist priority after customer feedback
```

## Combining Methods

For comprehensive prioritization, methods can be combined:

```bash
# Step 1: Quick MoSCoW categorization
/requirements-elicitation:prioritize --domain "checkout" --method moscow

# Step 2: WSJF ranking within SHOULD category
/requirements-elicitation:prioritize --domain "checkout" --method wsjf --filter "category:should"

# Step 3: Validate with stakeholder Kano analysis
/requirements-elicitation:prioritize --domain "checkout" --method kano --interactive
```

## Skills Used

This command uses the `prioritization-methods` skill for:

- Method selection guidance
- Scoring frameworks and formulas
- Interpretation criteria
- Output formatting

## Integration with Other Commands

### Before Prioritization

```bash
# Elicit requirements first
/requirements-elicitation:discover "checkout flow"

# Identify gaps
/requirements-elicitation:gaps --domain "checkout"
```

### After Prioritization

```bash
# Export prioritized requirements
/requirements-elicitation:export --domain "checkout" --filter "rank:1-10"

# Create story map with priorities
/requirements-elicitation:story-map --domain "checkout"
```

## Output Locations

```yaml
output_locations:
  yaml: ".requirements/{domain}/prioritization/PRIO-{timestamp}.yaml"
  markdown: ".requirements/{domain}/prioritization/PRIO-{timestamp}.md"
```

## Error Handling

```yaml
error_handling:
  no_requirements:
    message: "No requirements found for domain"
    action: "Run /discover first to elicit requirements"

  insufficient_data:
    message: "Cannot apply {method} - missing required data"
    action: "Switch to simpler method or gather missing scores"

  conflicting_scores:
    message: "Stakeholder scores significantly diverge"
    action: "Facilitate discussion to reach consensus"
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/melodic-software) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
