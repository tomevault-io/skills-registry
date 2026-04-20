---
name: test-case-generator
description: "Generates comprehensive, high-quality test cases for any domain or scenario. Covers positive, negative, edge-case, and security scenarios across UI, API, and Integration levels. Includes all essential attributes like Priority, Pre-conditions, and Expected Results."
version: 1.0.0
author: Antigravity
created: 2026-02-13
updated: 2026-02-13
platforms: [claude-code, codex, github-copilot-cli]
category: testing
tags: [qa, test-cases, manual-testing, automation-planning, quality-assurance]
risk: safe
---

# Test Case Generator

## Purpose

To automate the brainstorming and documentation of test cases. This skill ensures that your testing coverage is 100% by systematically generating positive and negative scenarios, edge cases, and cross-functional tests (Performance, Security, Accessibility) for any given feature or domain.

## When to Use

Use this skill when:
- Designing test suites for a new feature.
- Planning regression testing for an existing module.
- Brainstorming edge cases that might be missed during initial development.
- Documenting test scenarios for a sprint or a technical interview.

## Core Capabilities

1. **Comprehensive Extraction**: Analyzes the domain name or scenario to identify core entities and workflows.
2. **Multi-Perspective Testing**: Generates test cases from different angles:
   - **Positive**: Standard "happy path" workflows.
   - **Negative**: Error handling, invalid inputs, and security constraints.
   - **Boundary/Edge**: Data limits, race conditions, and extreme states.
3. **Attribute-Rich Documentation**: Includes Case ID, Description, Pre-conditions, Steps, Expected Results, Priority, and Test Type.
4. **Structured Output**: Formats results as a table (Markdown or CSV) for easy import into tools like Jira or Excel.

## Workflow

### Phase 1: Scenario Input
Ask the user for:
- **Feature/Domain Name**: e.g., "Login System" or "E-commerce Checkout."
- **Testing Scope**: e.g., "Only UI" or "End-to-end including API."

### Phase 2: Scenario Expansion
Generate a list of scenarios covering:
- Functional (Happy Path)
- Validation (Negative/Inverse Path)
- Boundary Value Analysis (BVA)
- Error Guessing
- Security & Permission checks
- UI/UX & Responsive checks

### Phase 3: Detailed Test Case Generation
Format each scenario into a detailed test case structure. Use the following attributes:

| TC ID | Scenario | Priority | Pre-conditions | Test Steps | Expected Result | Scenario Type |
|-------|----------|----------|----------------|------------|-----------------|---------------|
| TC_01 | ... | P0 | ... | 1. ... 2. ... | ... | Positive |

## Quality Standards

- **Independence**: Each test case must be atomic (not dependent on another test case's success).
- **Clarity**: Steps must be unambiguous and reproducible.
- **Traceability**: Link test cases to core requirements where possible.
- **Coverage**: Ensure at least 30% of cases are negative/edge-case scenarios.

---

## References

- **IEEE 829 Standard**: Standard for Software Test Documentation.
- **ISTQB Glossary**: Definitions for different testing techniques.
- **Template**: `resources/templates/test-case-template.md`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/marufcode) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
