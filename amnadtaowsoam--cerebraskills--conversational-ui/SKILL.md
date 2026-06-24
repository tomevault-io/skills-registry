---
name: conversational-ui
description: Conversational UIs provide natural language interfaces for AI-powered Use when this capability is needed.
metadata:
  author: amnadtaowsoam
---

# Conversational Ui

## Skill Profile
*(Select at least one profile to enable specific modules)*
- [ ] **DevOps**
- [x] **Backend**
- [ ] **Frontend**
- [ ] **AI-RAG**
- [ ] **Security Critical**

## Overview
Conversational UIs provide natural language interfaces for AI-powered applications, enabling users to interact through chat, voice, and multi-modal communication. They combine natural language understanding, context management, and intuitive design to deliver seamless, human-like interactions.

## Why This Matters
- **Reduces Friction**: Natural, intuitive interfaces lower barriers to entry and reduce user effort
- **Increases Engagement**: Multi-modal interactions (text, voice, visual) enhance user experience and satisfaction
- **Improves Accessibility**: Voice and chat interfaces make applications accessible to users with disabilities
- **Supports Scalability**: Automated interactions handle increasing user volume without proportional support costs
- **Provides Consistent Experience**: Standardized responses ensure uniform quality across all interactions
- **Enables Personalization**: Context-aware conversations adapt to user preferences and history

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
  - User messages (text, voice transcripts)
  - Voice input (audio stream)
  - Image uploads (files)
  - User interactions (clicks, selections)
* **Entry Conditions**:
  - Browser supports Web Speech API (for voice features)
  - React/Next.js application initialized
  - Backend chat API endpoint available
  - CSS framework configured (Tailwind CSS, etc.)
* **Outputs**:
  - Chat interface UI components
  - Voice recognition transcripts
  - Text-to-speech output
  - User interaction events
* **Artifacts Required (Deliverables)**:
  - Chat interface components
  - Voice recognition components
  - Text-to-speech components
  - Multi-modal components
  - Accessibility implementations
* **Acceptance Evidence**:
  - Component tests passing
  - Accessibility audit results (WCAG 2.1 AA)
  - Cross-browser testing results
  - Mobile responsiveness verified
* **Success Criteria**:
  - All interactive elements keyboard accessible
  - Screen reader announcements working
  - Voice recognition accuracy > 80%
  - Mobile responsive on all breakpoints
  - Lighthouse accessibility score > 90

## Skill Composition
* **Depends on**: [chatbot-integration](./chatbot-integration/SKILL.md), [llm-integration](../06-ai-ml-production/llm-integration/SKILL.md)
* **Compatible with**: [ai-agents](./ai-agents/SKILL.md), [ai-search](./ai-search/SKILL.md), [line-platform-integration](./line-platform-integration/SKILL.md)
* **Conflicts with**: Simple form-based UIs (different interaction model)
* **Related Skills**: [accessibility](../22-ux-ui-design/accessibility/SKILL.md), [responsive-design](../22-ux-ui-design/responsive-design/SKILL.md), [thai-ux-patterns](../22-ux-ui-design/thai-ux-patterns/SKILL.md)

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
