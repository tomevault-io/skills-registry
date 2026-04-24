---
name: linting-standards
description: | Use when this capability is needed.
metadata:
  author: spallempati
---

# Enforce Coding Standards via Linting Thresholds

## Description

This rule enforces that all code must pass language-specific linting tools with
zero blocker issues and no more than 5 minor warnings per 1,000 lines of code
(LoC). This ensures maintainability, readability, and security consistency
across the codebase.

## Purpose

To maintain a clean, standardized codebase that is easier to maintain, reduces
risk of defects, and aligns with secure coding practices. Linting also enforces
best practices and prevents unsafe patterns that may otherwise go unnoticed.

## Scope

- Java, Kotlin, Python, TypeScript, JavaScript, and Vue projects
- All application and infrastructure repositories
- PR-level enforcement during CI
- Applies to all engineers committing or reviewing code

## SDLC Integration

- **Planning**: Teams define required linting configurations
- **Analysis**: Static analysis identifies violations
- **Design**: Encourages idiomatic and defensively written code
- **Development**: CI lint checks block problematic code
- **Testing**: Automated verification of rule compliance
- **Deployment**: Prevents high-risk patterns from promotion
- **Maintenance**: Reduces technical debt via clean coding policies

## Standards

### Linter Compliance Thresholds

- Code **MUST** report 0 blocker-level linter violations (e.g.,
  `no-unsafe-innerHTML`)
- Code **SHOULD NOT** exceed 5 minor warnings per 1,000 LoC (≤ 0.5% warning
  density)
- Linting **MUST** be run automatically during CI
- Unsafe or deprecated patterns **MUST NOT** appear in committed code

## Actionable Metrics

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/spallempati) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
