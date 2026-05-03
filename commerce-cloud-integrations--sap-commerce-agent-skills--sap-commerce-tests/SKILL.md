---
name: sap-commerce-tests
description: Use for SAP Commerce unit-test implementation or review with JUnit 4 + Mockito conventions, plus single-test JaCoCo coverage execution and reporting through the provided Ant wrapper script. Use when this capability is needed.
metadata:
  author: commerce-cloud-integrations
---

# SAP Commerce Unit Tests

## Overview

Use this skill to create or update SAP Commerce unit tests and produce verifiable single-test coverage output.

## Trigger Checklist

Use this skill when one or more are true:

- writing new unit tests for SAP Commerce classes
- updating/refactoring existing unit tests
- reviewing test quality against project conventions
- running one test with coverage and reporting results

## Progressive Disclosure

Load only what the task needs:

- `references/test-conventions.md` for naming, structure, mocking rules
- `references/coverage-workflow.md` for run command and expected outputs
- `references/gotchas.md` for frequent run-time failures
- `scripts/run_single_test_with_coverage.sh` for deterministic execution

## Workflow

1. Locate or create test class under extension `testsrc`.
2. Apply naming and Arrange/Act/Assert conventions.
3. Register in `*-testclasses.xml` if required by the repo.
4. Run single-test coverage script.
5. Report JUnit and coverage metrics with HTML report paths.

## Quality Rules

- Keep one scenario per test method.
- Prefer real DTO/data objects; mock behavior dependencies.
- Avoid static mocking patterns.
- Keep assertions explicit and scenario-specific.

## Reporting Requirements

Always include:

- JUnit results (tests, failures, errors, time)
- extension coverage metrics
- target-class coverage metrics
- generated report paths

If execution fails, report the exact failure reason and stop claiming pass status.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/commerce-cloud-integrations) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
