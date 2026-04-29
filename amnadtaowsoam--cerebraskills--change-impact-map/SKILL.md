---
name: change-impact-map
description: Documentation showing "if we change A, what B/C are affected" - dependency Use when this capability is needed.
metadata:
  author: amnadtaowsoam
---

# Change Impact Map

## Skill Profile
*(Select at least one profile to enable specific modules)*
- [ ] **DevOps**
- [x] **Backend**
- [ ] **Frontend**
- [ ] **AI-RAG**
- [ ] **Security Critical**

## Overview
Documentation showing "if we change A, what B/C are affected" - dependency relationships between components that help assess change impact before implementation.

## Why This Matters
- **Risk assessment**: Know what breaks after a change
- **Test coverage**: Know what to test
- **Review scope**: Know who needs to review
- **Rollback planning**: Know what to rollback

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
- Codebase has clear architecture
- Dependencies can be mapped
- Stakeholders are known
- Change management process exists
- Testing capabilities available
- Monitoring and observability in place

## Compatibility
- **Languages**: All programming languages
- **Architectures**: Monolith, microservices, serverless
- **Deployment**: Kubernetes, VMs, serverless
- **Tools**: Git, CI/CD, monitoring

## Test Scenario Matrix
| Scenario | Input | Expected Output | Verification |
|----------|-------|-----------------|--------------|
| Code change | File/module | Dependency map | All deps identified |
| Service change | Service spec | Impact matrix | Direct/indirect mapped |
| Schema change | Migration plan | Critical paths | High-impact paths found |
| Config change | Environment vars | Affected services | All consumers identified |

## Technical Guardrails
#

## Agent Directives & Error Recovery
*(ข้อกำหนดสำหรับ AI Agent ในการคิดและแก้ปัญหาเมื่อเกิดข้อผิดพลาด)*

- **Thinking Process**: Analyze root cause before fixing. Do not brute-force.
- **Fallback Strategy**: Stop after 3 failed test attempts. Output root cause and ask for human intervention/clarification.
- **Self-Review**: Check against Guardrails & Anti-patterns before finalizing.
- **Output Constraints**: Output ONLY the modified code block. Do not explain unless asked.


## Definition of Done
Change impact analysis is complete when:

- [ ] All dependencies mapped (code, runtime, data, integration)
- [ ] Impact categories assessed (direct, indirect, operational, user-facing)
- [ ] Critical paths identified
- [ ] Blast radius evaluated
- [ ] Change scopes defined (small, medium, large)
- [ ] Stakeholders identified and communicated
- [ ] Mitigation strategies documented
- [ ] Test requirements defined
- [ ] Rollback procedures planned
- [ ] IMPACT_MAP.md created

## Anti-patterns
1. **No impact assessment**: "It's just a small change"
2. **Missing dependencies**: Hidden coupling not documented
3. **Outdated map**: Doesn't reflect current architecture
4. **No ownership**: No one responsible for areas
5. **No mitigation**: No rollback plan
6. **Poor communication**: Stakeholders not informed
7. **Critical paths ignored**: P0/P1 paths not protected
8. **Blast radius unknown**: Domino effects not considered

## Reference Links
- [Dependency Mapping](https://martinfowler.com/bliki/Strangler.html)
- [Change Management](https://www.atlassian.com/itsm/change-management)
- [Architecture Decision Records](https://adr.github.io/)

## Versioning & Changelog

* **Version**: 1.0.0
* **Changelog**:
  - 2026-02-22: Initial version with complete template structure

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/amnadtaowsoam) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
