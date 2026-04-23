---
name: evaluation
description: This skill should be used when the user asks to "evaluate agent performance", "build test framework", "measure agent quality", "create evaluation rubrics", or mentions agent testing or quality gates for agent pipelines. Use when this capability is needed.
metadata:
  author: bthillerup
---

# Evaluation Methods for Agent Systems

Agent evaluation requires different approaches than traditional software. Agents make dynamic decisions, are non-deterministic, and often lack single correct answers.

## When to Activate

Activate this skill when:
- Testing agent performance systematically
- Validating context engineering choices
- Building quality gates for agent pipelines
- Comparing different agent configurations

## Core Concepts

### The 95% Finding
Research found that three factors explain 95% of performance variance:

| Factor | Variance Explained |
|--------|-------------------|
| Token usage | 80% |
| Number of tool calls | ~10% |
| Model choice | ~5% |

**Implication**: Model upgrades often provide larger gains than doubling token budgets.

### Evaluation Challenges

**Non-Determinism**: Agents may take completely different valid paths to reach goals. Evaluate outcomes, not specific steps.

**Context-Dependent Failures**: Failures may emerge only after extended interaction. Test with realistic context sizes.

**Composite Quality**: Agent quality spans multiple dimensions that require separate evaluation.

## Multi-Dimensional Rubric

| Dimension | Description |
|-----------|-------------|
| Factual accuracy | Claims match ground truth |
| Completeness | Output covers requested aspects |
| Citation accuracy | Citations match claimed sources |
| Source quality | Uses appropriate primary sources |
| Tool efficiency | Uses right tools reasonable number of times |

## Evaluation Methodologies

### LLM-as-Judge
Scales to large test sets with consistent judgments. Design prompts that capture dimensions of interest.

### Human Evaluation
Catches what automation misses: hallucinated answers on unusual queries, system failures, subtle biases.

### End-State Evaluation
For agents that mutate persistent state, focus on whether final state matches expectations.

## Test Set Design

### Complexity Stratification

| Level | Description |
|-------|-------------|
| Simple | Single tool call |
| Medium | Multiple tool calls |
| Complex | Many tool calls, significant ambiguity |
| Very Complex | Extended interaction, deep reasoning |

Start with small samples during development—changes have dramatic impacts early.

## Context Engineering Evaluation

Run agents with different context strategies on the same test set. Compare quality scores, token usage, and efficiency metrics.

**Degradation Testing**: Test at different context sizes to identify performance cliffs.

## Guidelines

1. Use multi-dimensional rubrics, not single metrics
2. Evaluate outcomes, not specific execution paths
3. Cover complexity levels from simple to complex
4. Test with realistic context sizes and histories
5. Run evaluations continuously, not just before release
6. Supplement LLM evaluation with human review
7. Track metrics over time for trend detection
8. Set clear pass/fail thresholds based on use case

---

**Created**: 2025-12-20 | **Version**: 1.0.0

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/bthillerup) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
