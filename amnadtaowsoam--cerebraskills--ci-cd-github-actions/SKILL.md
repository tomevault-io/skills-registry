---
name: ci-cd-github-actions
description: GitHub Actions is a CI/CD platform that automates your build, test, and Use when this capability is needed.
metadata:
  author: AmnadTaowsoam
---

# Ci Cd Github Actions

## Skill Profile
*(Select at least one profile to enable specific modules)*
- [ ] **DevOps**
- [x] **Backend**
- [ ] **Frontend**
- [ ] **AI-RAG**
- [ ] **Security Critical**

## Overview
GitHub Actions is a CI/CD platform that automates your build, test, and deployment workflows. This skill covers workflow syntax, common patterns, matrix builds, caching, secrets management, environments, deployment strategies (blue-green, canary), monorepo support, reusable workflows, security best practices, and production examples.

## Why This Matters
- **Automation**: Automate build, test, and deployment processes
- **Integration**: Native GitHub integration with seamless workflow triggers
- **Flexibility**: Support for complex workflows with matrix builds, caching, and reusable workflows
- **Security**: Built-in secrets management and environment protection rules

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
  - Repository with workflow files (.github/workflows/*.yml)
  - Application code with build/test scripts
  - Deployment configuration (Kubernetes manifests, Docker images)
  - Secrets and environment variables
  - Service accounts for cloud providers

* **Entry Conditions**:
  - GitHub repository configured
  - Workflow files created in .github/workflows/
  - Build and test scripts available
  - Deployment targets configured

* **Outputs**:
  - Automated CI/CD pipelines
  - Built artifacts (Docker images, dist files)
  - Test reports and coverage
  - Deployed applications
  - Workflow status and notifications

* **Artifacts Required (Deliverables)**:
  - Workflow YAML files (.github/workflows/*.yml)
  - Build scripts (package.json scripts, Makefile)
  - Deployment manifests (Kubernetes, Docker Compose)
  - Environment configuration files
  - Documentation for workflow usage

* **Acceptance Evidence**:
  - Screenshot of GitHub Actions workflow runs
  - Screenshot of successful deployment
  - Screenshot of test results
  - Screenshot of workflow logs

* **Success Criteria**:
  - Workflows trigger correctly on events
  - Build and test jobs complete successfully
  - Artifacts are uploaded and accessible
  - Deployments complete without errors
  - Notifications are sent on failure

## Skill Composition
* **Depends on**: [deployment-patterns](./deployment-patterns/SKILL.md), [docker-patterns](./docker-patterns/SKILL.md), [kubernetes-deployment](./kubernetes-deployment/SKILL.md)
* **Compatible with**: [terraform-infrastructure](./terraform-infrastructure/SKILL.md), [gitops-argocd](./gitops-argocd/SKILL.md), [helm-charts](./helm-charts/SKILL.md)
* **Conflicts with**: None - GitHub Actions can coexist with other CI/CD tools
* **Related Skills**: [kubernetes-platform](../../62-scale-operations/kubernetes-platform/SKILL.md), [docker-deployment](../../69-platform-engineering-lite/deployment-patterns/SKILL.md)

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
> Source: [AmnadTaowsoam/CerebraSkills](https://github.com/AmnadTaowsoam/CerebraSkills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
