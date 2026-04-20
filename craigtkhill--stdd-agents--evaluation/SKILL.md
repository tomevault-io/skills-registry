---
name: evaluation
description: Use when creating or updating agent evaluation suites. Defines eval structure, rubrics, and validation patterns.
metadata:
  author: craigtkhill
---

# Evaluation Skill

Guidelines for creating comprehensive evaluation suites.

## When to Use This Skill

Use this skill when:
- Creating a NEW evaluation suite for a feature
- Updating an EXISTING evaluation suite
- Understanding the evaluation framework patterns
- Writing spec.yaml, rubric.md, or evaluation files

## Evaluation Framework Overview

All evaluations in `evals/` follow a consistent structure with both **code-based** and **LLM-as-judge** validations.

## spec.yaml Template

Use this template for all eval spec.yaml files:

```yaml
feature:
  name: "[Feature Name] Evaluation"
  as_a: evaluator
  i_want: validate feature behavior
  solutions:
    - Ground truth validation
    - Code-based validation
    - LLM-as-judge validation

requirements:
  - id: REQ-EVAL-XX-001
    eval: G
    description: Description of ground truth requirement
  - id: REQ-EVAL-XX-002
    eval: C
    description: Description of code-based requirement
  - id: REQ-EVAL-XX-003
    eval: L
    description: Description of LLM-judged requirement
  - id: REQ-EVAL-XX-004
    eval: O
    description: Description of planned requirement
```

**Template Rules:**
- **Identifier Format**: `REQ-EVAL-XX-NNN`
  - `XX` = 2-3 letter eval abbreviation (e.g., AG for action_generation, AS for action_scenarios)
  - `NNN` = Sequential 3-digit number starting at 001
- **Implementation Types**:
  - `[G]` = Ground truth validation (matches expected output)
  - `[C]` = Code-based validation (deterministic checks)
  - `[L]` = LLM-as-judge validation (quality assessment)
  - `[O]` = Not yet implemented (planned for future)
- **Categories**: Group related requirements logically

## rubric.md Template

Use this template for all rubric.md files:

```markdown
# [Feature Name] Reasoning Trace Rubric

## Format
`[PASS/FAIL] RUBRIC-ID: Criterion description`

## Based on: [Concrete example with specific values]

### [Category Name]
- [ ] RUB-XX-001: Specific, objective criterion
- [ ] RUB-XX-002: Another specific criterion
```

**Template Rules:**
- **Identifier Format**: `RUB-XX-NNN` (matches spec.yaml abbreviation)
- **Categories**: Organize criteria into logical groups
- **Criteria**: Write concrete, objectively verifiable rules, not subjective assessments
- **Specificity**: Reference actual values, fields, or behaviors that can be checked
- **Checkboxes**: Use `- [ ]` format for LLM judge to mark pass/fail
- **Avoid subjective language**: Do not use vague terms; state exactly what to verify

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/craigtkhill) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
