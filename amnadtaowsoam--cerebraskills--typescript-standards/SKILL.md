---
name: typescript-standards
description: TypeScript coding standards provide comprehensive guidelines for writing Use when this capability is needed.
metadata:
  author: amnadtaowsoam
---

# Typescript Standards

## Skill Profile
*(Select at least one profile to enable specific modules)*
- [ ] **DevOps**
- [x] **Backend**
- [ ] **Frontend**
- [ ] **AI-RAG**
- [ ] **Security Critical**

## Overview
TypeScript coding standards provide comprehensive guidelines for writing type-safe, maintainable code. This skill covers strict TypeScript configuration, naming conventions, type definitions, error handling patterns, async/await best practices, utility types, dependency injection, and environment variable validation for both Backend (Node.js) and Frontend (Next.js) projects.

## Why This Matters
- **Improves Code Quality**: Type hints and validation catch errors early; strict mode ensures consistent, readable code
- **Reduces Bugs**: Comprehensive error handling and proper validation prevent runtime exceptions and data corruption
- **Enhances Maintainability**: Clean code patterns and proper abstractions make code easier to understand, modify, and extend
- **Enables Better Tooling**: Type hints improve IDE autocomplete and error detection; linters catch issues automatically
- **Supports AI/ML Development**: Modern TypeScript patterns (async, type hints, dataclasses) are essential for AI/ML workflows
- **Facilitates Team Collaboration**: Consistent coding standards make code reviews more effective and onboarding smoother for new team members

---

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
  - TypeScript source files
  - Configuration files (tsconfig.json)
  - Requirements and dependencies
  - API specifications
* **Entry Conditions**:
  - TypeScript 5+ environment is configured
  - Required packages are installed
  - Project structure is established
* **Outputs**:
  - Type-safe, strict-mode TypeScript code
  - Comprehensive type definitions
  - Validated data models
  - Proper error handling
  - Clean import structure
* **Artifacts Required (Deliverables)**:
  - TypeScript source code
  - Type definition files (.d.ts if needed)
  - Configuration files (tsconfig.json)
  - ESLint and Prettier configs
* **Acceptance Evidence**:
  - Code passes TypeScript strict mode compilation
  - Code passes ESLint with TypeScript rules
  - Code is properly formatted with Prettier
  - No `any` types used inappropriately
  - Proper error handling implemented
* **Success Criteria**:
  - Code is type-safe and maintainable
  - Type hints are comprehensive
  - Error handling is comprehensive
  - Import structure is clean

## Skill Composition
* **Depends on**: [code-review](../code-review/SKILL.md), [api-design](../api-design/SKILL.md)
* **Compatible with**: [refactoring-strategies](../refactoring-strategies/SKILL.md), [git-workflow](../git-workflow/SKILL.md)
* **Conflicts with**: None
* **Related Skills**: [python-standards](../python-standards/SKILL.md), [nextjs-patterns](../02-frontend/nextjs-patterns/SKILL.md)

---

## Quick Start / Implementation Example

1. Review requirements and constraints
2. Set up development environment
3. Implement core functionality following patterns
4. Write tests for critical paths
5. Run tests and fix issues
6. Document any deviations or decisions

```python
# Example implementation following best practices
def example_function():
    # Your implementation here
    pass
```


## Assumptions / Constraints / Non-goals

* **Assumptions**:
  - Development environment is properly configured
  - Required dependencies are available
  - Team has basic understanding of domain
* **Constraints**:
  - Must follow existing codebase conventions
  - Time and resource limitations
  - Compatibility requirements
* **Non-goals**:
  - This skill does not cover edge cases outside scope
  - Not a replacement for formal training


## Compatibility & Prerequisites

* **Supported Versions**:
  - Python 3.8+
  - Node.js 16+
  - Modern browsers (Chrome, Firefox, Safari, Edge)
* **Required AI Tools**:
  - Code editor (VS Code recommended)
  - Testing framework appropriate for language
  - Version control (Git)
* **Dependencies**:
  - Language-specific package manager
  - Build tools
  - Testing libraries
* **Environment Setup**:
  - `.env.example` keys: `API_KEY`, `DATABASE_URL` (no values)


## Test Scenario Matrix (QA Strategy)

| Type | Focus Area | Required Scenarios / Mocks |
| :--- | :--- | :--- |
| **Unit** | Core Logic | Must cover primary logic and at least 3 edge/error cases. Target minimum 80% coverage |
| **Integration** | DB / API | All external API calls or database connections must be mocked during unit tests |
| **E2E** | User Journey | Critical user flows to test |
| **Performance** | Latency / Load | Benchmark requirements |
| **Security** | Vuln / Auth | SAST/DAST or dependency audit |
| **Frontend** | UX / A11y | Accessibility checklist (WCAG), Performance Budget (Lighthouse score) |


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


## Definition of Done (DoD) Checklist

- [ ] Tests passed + coverage met
- [ ] Lint/Typecheck passed
- [ ] Logging/Metrics/Trace implemented
- [ ] Security checks passed
- [ ] Documentation/Changelog updated
- [ ] Accessibility/Performance requirements met (if frontend)


## Anti-patterns / Pitfalls

* ⛔ **Don't**: Log PII, catch-all exception, N+1 queries
* ⚠️ **Watch out for**: Common symptoms and quick fixes
* 💡 **Instead**: Use proper error handling, pagination, and logging


## Reference Links & Examples

* Internal documentation and examples
* Official documentation and best practices
* Community resources and discussions


## Versioning & Changelog

* **Version**: 1.0.0
* **Changelog**:
  - 2026-02-22: Initial version with complete template structure

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/amnadtaowsoam) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
