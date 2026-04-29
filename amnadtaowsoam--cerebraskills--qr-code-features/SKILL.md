---
name: qr-code-features
description: QR code generation, scanning, validation, and use cases for events, payments, Use when this capability is needed.
metadata:
  author: amnadtaowsoam
---

# Qr Code Features

## Skill Profile
*(Select at least one profile to enable specific modules)*
- [ ] **DevOps**
- [x] **Backend**
- [ ] **Frontend**
- [ ] **AI-RAG**
- [ ] **Security Critical**

## Overview
QR code generation, scanning, validation, and use cases for events, payments, and guest management. This skill covers various QR code formats, security measures, dynamic QR codes, and integration with event management systems.

## Why This Matters
QR codes are critical for:

- **Contactless Experience**: Fast, touchless interactions for health and safety
- **Cost Efficiency**: Reduce printing, material, and operational costs
- **Versatility**: Work across devices and platforms without app requirements
- **Security**: Signed and encrypted QR codes prevent fraud and unauthorized access
- **Analytics**: Track scan patterns, usage statistics, and engagement metrics
- **Offline Capability**: Function without internet connectivity for static codes

Poor QR code implementation leads to:
- Unreliable scanning and poor user experience
- Security vulnerabilities from unsigned codes
- Fraud and unauthorized access
- Inability to track usage or attendance
- Poor accessibility for users without cameras

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
- Users have camera-equipped devices for scanning
- Network connectivity available for dynamic QR codes
- QR code libraries are compatible with target platforms
- Lighting conditions allow for reliable scanning
- Users understand how to use QR codes

## Compatibility
- Works with all modern browsers (Chrome, Firefox, Safari, Edge)
- Compatible with iOS and Android devices
- Supports React, Vue, Angular, and vanilla JS
- Compatible with Node.js and browser environments
- Works with various QR code scanners

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
**When Generating QR Codes:**
1. Determine appropriate QR code type for use case
2. Create data payload with required fields
3. Sign or encrypt sensitive data
4. Set appropriate expiry time
5. Set scan limit if needed
6. Generate QR code with proper error correction
7. Store QR code record in database

**When Scanning QR Codes:**
1. Request camera permissions appropriately
2. Handle permission denials gracefully
3. Scan continuously until QR code detected
4. Validate scanned data (signature, expiry)
5. Record scan for analytics
6. Provide fallback for manual entry

**When Managing QR Code Security:**
1. Use HMAC signing for authenticity
2. Implement expiry times
3. Set scan limits for one-time use
4. Track all scans for fraud detection
5. Rotate signing keys periodically
6. Never expose keys in client code

## Definition of Done (DoD) Checklist

- [ ] Tests passed + coverage met
- [ ] Lint/Typecheck passed
- [ ] Logging/Metrics/Trace implemented
- [ ] Security checks passed
- [ ] Documentation/Changelog updated
- [ ] Accessibility/Performance requirements met (if frontend)


## Anti-patterns
**QR Code Too Small**
- QR codes under 200x200px are difficult to scan
- Poor user experience
- Increased failure rate

**Low Error Correction**
- Using error correction level L fails with damaged codes
- Poor reliability in real-world conditions
- Increased failure rate

**No Expiry**
- QR codes without expiry create security vulnerabilities
- Indefinite validity
- Increased fraud risk

**Missing Validation**
- Not validating scanned data leads to accepting fraudulent codes
- Security vulnerabilities
- Poor user experience

**Poor Camera Handling**
- Not handling camera permission denials blocks users
- Poor accessibility
- User frustration

**Over-Styling**
- Too much customization (logos, colors) reduces scannability
- Poor reliability
- Increased failure rate

**No Fallback**
- Not providing manual entry option when scanning fails
- Poor accessibility
- User frustration

**Hardcoded Secrets**
- Embedding signing keys in client code compromises security
- Security vulnerability
- Fraudulent QR code generation

**No Analytics**
- Not tracking scans prevents fraud detection and optimization
- No visibility into usage
- Inability to detect issues

**Ignoring Offline**
- Not considering offline scenarios limits reliability
- Poor user experience
- Increased failure rate

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
