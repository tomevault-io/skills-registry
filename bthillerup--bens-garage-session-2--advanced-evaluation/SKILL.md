---
name: advanced-evaluation
description: This skill should be used when the user asks to "implement LLM-as-judge", "compare model outputs", "create evaluation rubrics", "mitigate evaluation bias", or mentions direct scoring, pairwise comparison, or automated quality assessment. Use when this capability is needed.
metadata:
  author: bthillerup
---

# Advanced Evaluation

Production-grade techniques for evaluating LLM outputs using LLMs as judges. LLM-as-a-Judge is a family of approaches, each suited to different evaluation contexts.

## When to Activate

Activate this skill when:
- Building automated evaluation pipelines
- Comparing multiple model responses
- Establishing consistent quality standards
- Debugging evaluation systems with inconsistent results

## Evaluation Taxonomy

### Direct Scoring
Single LLM rates one response on a defined scale.
- **Best for**: Objective criteria (factual accuracy, instruction following)
- **Reliability**: Moderate to high for well-defined criteria
- **Failure mode**: Score calibration drift

### Pairwise Comparison
LLM compares two responses and selects the better one.
- **Best for**: Subjective preferences (tone, style)
- **Reliability**: Higher than direct scoring for preferences
- **Failure mode**: Position bias, length bias

## The Bias Landscape

| Bias | Description | Mitigation |
|------|-------------|------------|
| Position | First-position responses preferred | Evaluate twice with swapped positions |
| Length | Longer responses rated higher | Explicit prompting to ignore length |
| Self-Enhancement | Models rate own outputs higher | Use different models for generation/evaluation |
| Verbosity | Detailed explanations rated higher | Criteria-specific rubrics |
| Authority | Confident tone rated higher | Require evidence citation |

## Direct Scoring Implementation

**Prompt Structure**:
```
You are an expert evaluator assessing response quality.

## Task
Evaluate the following response against each criterion.

## Original Prompt
{prompt}

## Response to Evaluate
{response}

## Criteria
{for each: name, description, weight}

## Instructions
For each criterion:
1. Find specific evidence in the response
2. Score according to the rubric (1-{max} scale)
3. Justify your score with evidence
```

**Critical**: Require justification BEFORE score. Improves reliability by 15-25%.

## Pairwise Comparison Implementation

**Position Bias Mitigation Protocol**:
1. First pass: A in first position, B in second
2. Second pass: B in first position, A in second
3. If passes disagree: return TIE with reduced confidence
4. If consistent: averaged confidence

## Rubric Generation

Well-defined rubrics reduce evaluation variance by 40-60%.

**Components**:
- Level descriptions: Clear boundaries for each score
- Characteristics: Observable features for each level
- Examples: Representative text (optional but valuable)
- Edge cases: Guidance for ambiguous situations

**Strictness Calibration**:
- Lenient: Encouraging iteration
- Balanced: Production use
- Strict: Safety-critical evaluation

## Decision Framework

```
Is there objective ground truth?
├── Yes → Direct Scoring
│   └── factual accuracy, format compliance
└── No → Is it preference/quality judgment?
    ├── Yes → Pairwise Comparison
    │   └── tone, style, creativity
    └── No → Reference-based evaluation
        └── summarization, translation
```

## Scaling Evaluation

### Panel of LLMs (PoLL)
Multiple models as judges, aggregate votes. Reduces individual model bias.

### Hierarchical Evaluation
Fast cheap model for screening, expensive model for edge cases.

### Human-in-the-Loop
Automated for clear cases, human review for low-confidence.

## Guidelines

1. Always require justification before scores
2. Always swap positions in pairwise comparison
3. Match scale granularity to rubric specificity
4. Separate objective and subjective criteria
5. Include confidence scores
6. Define edge cases explicitly
7. Use domain-specific rubrics
8. Validate against human judgments
9. Monitor for systematic bias
10. Design for iteration

---

**Created**: 2024-12-24 | **Version**: 1.0.0

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/bthillerup) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
