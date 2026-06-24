---
name: lint-format-typecheck
description: Linting, formatting, and type checking are automated code quality tools Use when this capability is needed.
metadata:
  author: amnadtaowsoam
---

# Lint Format Typecheck

## Skill Profile
*(Select at least one profile to enable specific modules)*
- [ ] **DevOps**
- [x] **Backend**
- [ ] **Frontend**
- [ ] **AI-RAG**
- [ ] **Security Critical**

## Overview
Linting, formatting, and type checking are automated code quality tools that catch errors, enforce style consistency, and improve code maintainability before code reaches production.

**Core Principle**: "Automate code quality checks so humans can focus on logic, not syntax."

---

## Why This Matters
- **<Benefit>**: <short explanation>
- **<Benefit>**: <short explanation>
- **<Benefit>**: <short explanation>

## Core Concepts & Rules

### 1. Core Principles
- Follow established patterns and conventions
- Maintain consistency across codebase
- Document decisions and trade-offs

### 2. Implementation Guidelines
- Start with the simplest viable solution
- Iterate based on feedback and requirements
- Test thoroughly before deployment


## Inputs / Outputs / Contracts
* **Inputs**:
  - <e.g., env vars, request payload, file paths, schema>
* **Entry Conditions**:
  - <Pre-requisites: e.g., Repo initialized, DB running, specific branch checked out>
* **Outputs**:
  - <e.g., artifacts (PR diff, docs, tests, dashboard JSON)>
* **Artifacts Required (Deliverables)**:
  - <e.g., Code Diff, Unit Tests, Migration Script, API Docs>
* **Acceptance Evidence**:
  - <e.g., Test Report (screenshot/log), Benchmark Result, Security Scan Report>
* **Success Criteria**:
  - <e.g., p95 < 300ms, coverage ≥ 80%>

## Skill Composition
* **Depends on**: None
* **Compatible with**: None
* **Conflicts with**: None
* **Related Skills**: None

## Quick Start
#

## Assumptions
- Team agrees on code style standards
- Development environment has required tools
- CI/CD pipeline is available

## Compatibility
- Works with most JavaScript/TypeScript frameworks
- Compatible with Python projects
- Can be integrated with any CI/CD system

---

## Test Scenario Matrix
| Scenario | Expected Behavior | Notes |
|----------|-------------------|-------|
| Save file | Auto-format on save | VS Code integration |
| Commit code | Pre-commit hooks run | Lint, format, type-check |
| Push to GitHub | CI checks run | All checks must pass |
| Type error | Caught before runtime | TypeScript/mypy |

---

## Technical Guardrails & Security Threat Model

### 1. Security & Privacy (Threat Model)
* **Top Threats**: Injection attacks, authentication bypass, data exposure
- [ ] **Data Handling**: Sanitize all user inputs to prevent Injection attacks. Never log raw PII
- [ ] **Secrets Management**: No hardcoded API keys. Use Env Vars/Secrets Manager
- [ ] **Authorization**: Validate user permissions before state changes

### 2. Performance & Resources
- [ ] **Execution Efficiency**: Consider time complexity for algorithms
- [ ] **Memory Management**: Use streams/pagination for large data
- [ ] **Resource Cleanup**: Close DB connections/file handlers in finally blocks

### 3. Architecture & Scalability
- [ ] **Design Pattern**: Follow SOLID principles, use Dependency Injection
- [ ] **Modularity**: Decouple logic from UI/Frameworks

### 4. Observability & Reliability
- [ ] **Logging Standards**: Structured JSON, include trace IDs `request_id`
- [ ] **Metrics**: Track `error_rate`, `latency`, `queue_depth`
- [ ] **Error Handling**: Standardized error codes, no bare except
- [ ] **Observability Artifacts**:
    - **Log Fields**: timestamp, level, message, request_id
    - **Metrics**: request_count, error_count, response_time
    - **Dashboards/Alerts**: High Error Rate > 5%


## Agent Directives & Error Recovery
*(ข้อกำหนดสำหรับ AI Agent ในการคิดและแก้ปัญหาเมื่อเกิดข้อผิดพลาด)*

- **Thinking Process**: Analyze root cause before fixing. Do not brute-force.
- **Fallback Strategy**: Stop after 3 failed test attempts. Output root cause and ask for human intervention/clarification.
- **Self-Review**: Check against Guardrails & Anti-patterns before finalizing.
- **Output Constraints**: Output ONLY the modified code block. Do not explain unless asked.


## Definition of Done
- [ ] Linter configured with team rules
- [ ] Formatter configured with consistent settings
- [ ] Type checker enabled with strict mode
- [ ] Pre-commit hooks set up
- [ ] CI/CD integration configured
- [ ] IDE integration enabled
- [ ] Rules documented for team
- [ ] Progressive adoption plan in place
- [ ] All checks passing

---

## Anti-patterns / Pitfalls

* ⛔ **Don't**: Log PII, catch-all exception, N+1 queries
* ⚠️ **Watch out for**: Common symptoms and quick fixes
* 💡 **Instead**: Use proper error handling, pagination, and logging


## Reference Links
- [ESLint Documentation](https://eslint.org/)
- [Prettier Documentation](https://prettier.io/)
- [TypeScript Documentation](https://www.typescriptlang.org/)
- [Pylint Documentation](https://pylint.pycqa.org/)
- [Black Documentation](https://black.readthedocs.io/)

---

## Versioning & Changelog

* **Version**: 1.0.0
* **Changelog**:
  - 2026-02-22: Initial version with complete template structure

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/amnadtaowsoam) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
