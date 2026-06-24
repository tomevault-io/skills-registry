---
name: testing
description: Design, execute, and report tests that verify implemented changes against requirements and policies. Use when this capability is needed.
metadata:
  author: alloveralls
---

## Purpose
Deliver test updates and results that validate the implemented changes and surface defects.

## Inputs
- Implemented changes and expected behavior
- Test strategy from plan/architecture
- Applicable policies (`testing_policy`, `guardrails`, `commit_pr_rules`) and environment details

## Steps
1. Load changes, requirements, and testing policies.
2. Identify coverage goals; design or update tests accordingly.
3. Execute relevant test suites; record commands and results.
4. Report defects to the reviewing flow; summarize coverage and gaps.

## Outputs
- Test cases or updates, executed results with commands, and defects routed to review.

## Failure modes
- Skipping required tests or policies
- Incomplete coverage for changed behavior
- Altering product logic beyond test scaffolding

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/alloveralls) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
