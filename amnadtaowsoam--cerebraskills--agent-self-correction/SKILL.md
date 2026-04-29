---
name: agent-self-correction
description: AI agent self-correction mechanisms enable agents to detect errors, validate Use when this capability is needed.
metadata:
  author: amnadtaowsoam
---

# Agent Self Correction

## Skill Profile
*(Select at least one profile to enable specific modules)*
- [ ] **DevOps**
- [x] **Backend**
- [ ] **Frontend**
- [ ] **AI-RAG**
- [ ] **Security Critical**

## Overview
AI agent self-correction mechanisms enable agents to detect errors, validate outputs, and automatically recover from failures. This includes validation loops, confidence scoring, iterative refinement, and recovery strategies to improve reliability.

## Why This Matters
- **Reliability**: Agents can correct errors themselves without human intervention
- **Quality**: Output quality improves through iterative refinement
- **Trust**: Users are confident in results through confidence scoring
- **Efficiency**: Reduce unnecessary retry loops through smart recovery

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
  - Task description or prompt
  - Expected output format (JSON, text, etc.)
  - Context information (facts, constraints)
  - Validation rules and thresholds
* **Entry Conditions**:
  - LLM API is accessible
  - Error detection patterns are defined
  - Recovery strategies are configured
* **Outputs**:
  - Validated and corrected outputs
  - Confidence scores
  - Error reports
  - Improvement metrics
* **Artifacts Required (Deliverables)**:
  - Error detector implementation
  - Validation loop implementation
  - Recovery strategy definitions
  - Self-reflection system
* **Acceptance Evidence**:
  - Errors are detected and corrected
  - Validation loops converge
  - Confidence scores improve over iterations
  - Recovery strategies work correctly
* **Success Criteria**:
  - Error detection accuracy > 90%
  - Validation convergence rate > 95%
  - Confidence scores correlate with quality
  - Recovery success rate > 80%

## Skill Composition
* **Depends on**: [skill-architect](../72-metacognitive-skill-architect/skill-architect/SKILL.md)
* **Compatible with**: [task-decomposition-strategy](../72-metacognitive-skill-architect/task-decomposition-strategy/SKILL.md)
* **Conflicts with**: Infinite validation loops, over-correction
* **Related Skills**: [skill-discovery-and-chaining](../72-metacognitive-skill-architect/skill-discovery-and-chaining/SKILL.md)

---

## Quick Start
```typescript
// 1. Set up error detection
const detector = new ErrorDetector()

// 2. Set up validation loop
const loop = new ValidationLoop()

// 3. Execute with self-correction
const result = await loop.executeWithValidation(
  () => llm.generate(task),
  (output) => {
    const errors = detector.detectErrors(output, context)
    return {
      isValid: errors.length === 0,
      errors: errors.map(e => e.message),
      warnings: [],
      confidence: 1.0 - (errors.length * 0.2),
    }
  },
  (output, errors) => llm.generate(`Fix: ${errors.join(', ')}\nOutput: ${output}`)
)
)
```

---

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
