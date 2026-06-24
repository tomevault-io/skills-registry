---
name: terraform-infrastructure
description: Terraform is an Infrastructure as Code (IaC) tool that allows you to Use when this capability is needed.
metadata:
  author: AmnadTaowsoam
---

# Terraform Infrastructure

## Skill Profile
*(Select at least one profile to enable specific modules)*
- [ ] **DevOps**
- [x] **Backend**
- [ ] **Frontend**
- [ ] **AI-RAG**
- [ ] **Security Critical**

## Overview
Terraform is an Infrastructure as Code (IaC) tool that allows you to define and provision cloud infrastructure. This skill covers Terraform basics, providers, resources, modules, state management, remote state, workspaces, and best practices for building reproducible, scalable infrastructure.

## Why This Matters
Terraform is critical for modern infrastructure because it:

- **Enables Infrastructure as Code** for reproducible deployments
- **Provides version control** for infrastructure changes
- **Supports multiple providers** (AWS, Azure, GCP, etc.)
- **Implements state management** for tracking resources
- **Enables collaboration** through shared state
- **Provides planning** before applying changes
- **Supports modules** for reusable infrastructure components

---

## Core Concepts
| Concept | Description |
|---------|-------------|
| **Provider** | Plugin for managing resources (AWS, Azure, GCP) |
| **Resource** | Infrastructure component to manage |
| **Variable** | Input parameters for configuration |
| **Output** | Values returned after apply |
| **Module** | Reusable collection of resources |
| **State** | Mapping of resources to configuration |
| **Workspace** | Separate state for different environments |

## Inputs / Outputs / Contracts
#

## Skill Composition
* **Depends on**: [git-workflow](..\..\01-foundations\git-workflow/SKILL.md)
* **Compatible with**: None
* **Conflicts with**: None
* **Related Skills**: [docker](..\docker-compose/SKILL.md), [kubernetes](..\kubernetes-deployment/SKILL.md)

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


## Assumptions
- Cloud provider account is configured with appropriate permissions
- Terraform CLI is installed
- Network connectivity to cloud provider APIs
- Team has basic understanding of cloud infrastructure
- Version control system (Git) is available

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


## Agent Directives
When using Terraform:

1. **Always run `terraform init`** before other commands
2. **Validate configuration** with `terraform validate`
3. **Review plan** with `terraform plan` before applying
4. **Use remote state** for team collaboration
5. **Encrypt sensitive data** in state
6. **Use modules** for reusable infrastructure
7. **Document all changes** in commit messages

## Definition of Done (DoD) Checklist

- [ ] Tests passed + coverage met
- [ ] Lint/Typecheck passed
- [ ] Logging/Metrics/Trace implemented
- [ ] Security checks passed
- [ ] Documentation/Changelog updated
- [ ] Accessibility/Performance requirements met (if frontend)


## Anti-patterns
1. **Hardcoding Values**: Not using variables for configuration
2. **No Remote State**: Using local state for team collaboration
3. **No State Locking**: Multiple users modifying state simultaneously
4. **Ignoring State Drift**: Not refreshing state regularly
5. **Large Monolithic Files**: Not using modules for organization
6. **Manual Changes**: Modifying resources outside Terraform

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
<!-- tomevault:4.0:skill_md:2026-06-16 -->
