---
name: thai-cultural-events
description: Domain knowledge for Thai ceremonies, rituals, and cultural practices Use when this capability is needed.
metadata:
  author: amnadtaowsoam
---

# Thai Cultural Events

## Skill Profile
*(Select at least one profile to enable specific modules)*
- [ ] **DevOps**
- [x] **Backend**
- [ ] **Frontend**
- [ ] **AI-RAG**
- [ ] **Security Critical**

## Overview
Domain knowledge for Thai ceremonies, rituals, and cultural practices including weddings, ordinations, funerals, merit-making ceremonies, and regional customs. This skill provides guidance for event planning platforms serving Thai cultural events, with respect for traditional practices and regional variations.

## Why This Matters
Thai cultural event planning is critical for:

- **Cultural Respect**: Understanding traditions ensures proper respect and authenticity
- **Regional Awareness**: Different Thai regions (Central, North, Northeast, South) have distinct customs
- **Vendor Coordination**: Proper knowledge enables coordination with temples, monks, and traditional vendors
- **Timing and Scheduling**: Auspicious dates, Buddhist holy days, and ceremonial timing require careful planning
- **Budget Accuracy**: Traditional ceremonies have specific requirements and costs that must be accurately estimated
- **User Trust**: Demonstrating cultural knowledge builds trust with Thai users

Poor Thai cultural event planning leads to:
- Cultural insensitivity or offense
- Missed opportunities due to timing conflicts
- Budget overruns from unexpected traditional requirements
- Vendor coordination failures
- Loss of trust from cultural misunderstandings

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
- Thai calendar and auspicious dates are available
- Temples and monks can be booked in advance
- Traditional vendors are available and reasonably priced
- Users understand and respect Thai cultural practices
- Regional variations are documented and understood
- Budget is sufficient for traditional ceremony requirements

## Compatibility
- Works with all event planning platforms
- Compatible with all backend frameworks
- Supports React, Vue, Angular, and vanilla JS
- Works in both Thai and English language contexts
- Compatible with various calendar and scheduling systems

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
**When Planning Thai Cultural Events:**
1. Research cultural requirements for the specific ceremony type
2. Identify appropriate region and regional customs
3. Calculate auspicious dates avoiding Buddhist holy days
4. Estimate budget including all traditional components
5. Book temple and monks well in advance
6. Source traditional items and vendors
7. Create detailed timeline for all ceremony phases
8. Coordinate timing between all vendors
9. Document all cultural decisions and rationale

**When Managing Regional Variations:**
1. Identify the specific region for the event
2. Apply appropriate regional customs and traditions
3. Source vendors familiar with regional practices
4. Use appropriate regional music, costumes, and cuisine
5. Respect regional price variations
6. Document regional differences for future reference

**When Coordinating with Temples:**
1. Book temples and monks 2-6 months in advance
2. Confirm availability for auspicious dates
3. Prepare offerings and donations
4. Coordinate timing with other vendors
5. Respect temple protocols and dress codes
6. Prepare appropriate donation amounts

**When Budgeting:**
1. Get quotes from multiple vendors for comparison
2. Include all traditional components (temple, monks, items)
3. Account for regional price variations
4. Add contingency for unexpected costs
5. Present detailed breakdown to client
6. Allow for adjustments based on budget

## Definition of Done (DoD) Checklist

- [ ] Tests passed + coverage met
- [ ] Lint/Typecheck passed
- [ ] Logging/Metrics/Trace implemented
- [ ] Security checks passed
- [ ] Documentation/Changelog updated
- [ ] Accessibility/Performance requirements met (if frontend)


## Anti-patterns
**Ignoring Buddhist Calendar**
- Not checking for Wan Phra (Buddhist holy days) can cause scheduling conflicts
- Missed opportunities due to date conflicts
- Cultural insensitivity

**Insufficient Monk Booking**
- Not booking enough monks or booking too late
- Temple unavailability on ceremony date
- Shortage of monks for ceremonies

**Cultural Misunderstandings**
- Not consulting with elders or cultural advisors
- Applying wrong regional customs
- Disrespectful practices

**Budget Underestimation**
- Forgetting traditional items or vendor costs
- Not accounting for regional price variations
- No contingency for unexpected costs

**Poor Vendor Coordination**
- Not confirming vendor availability
- No backup vendors for critical items
- Poor timing between vendors

**Late Temple Booking**
- Booking temples too late
- No availability on auspicious dates
- Temple fully booked for requested date

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
