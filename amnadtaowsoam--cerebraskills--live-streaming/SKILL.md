---
name: live-streaming
description: Live streaming enables real-time video broadcasting. This guide covers Use when this capability is needed.
metadata:
  author: amnadtaowsoam
---

# Live Streaming

## Skill Profile
*(Select at least one profile to enable specific modules)*
- [ ] **DevOps**
- [x] **Backend**
- [ ] **Frontend**
- [ ] **AI-RAG**
- [ ] **Security Critical**

## Overview
Live streaming enables real-time video broadcasting. This guide covers RTMP, HLS, WebRTC, streaming servers, and cloud platforms for building scalable live streaming solutions with appropriate latency and quality.

## Why This Matters
Live streaming is critical for video platforms as it directly impacts:
- **User Engagement**: Real-time interaction and participation
- **Reach**: Global audiences can watch simultaneously
- **Monetization**: Live ads, donations, and subscriptions
- **Experience**: Low latency enables real-time interaction

---

## Core Concepts
1. **RTMP (Real-Time Messaging Protocol)**: For stream ingestion from encoders
2. **HLS (HTTP Live Streaming)**: For playback with 10-30s latency
3. **WebRTC**: For ultra-low latency (< 1s) streaming
4. **Streaming Server**: NGINX-RTMP, Wowza, or cloud services
5. **Stream Key**: Authentication token for publishing streams
6. **Adaptive Bitrate**: Multiple quality levels for varying bandwidth
7. **Latency Modes**: Low, Normal, Ultra-low latency options

## Inputs / Outputs / Contracts
#

## Skill Composition
* **Depends on**: None
* **Compatible with**: None
* **Conflicts with**: None
* **Related Skills**: None

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
- Sufficient bandwidth for streaming quality
- Encoder software (OBS, FFmpeg) available
- CDN configured for playback distribution
- TURN/STUN servers for WebRTC

## Compatibility
| Protocol | Ingestion | Playback | Mobile | Desktop |
|----------|-----------|----------|---------|---------|
| RTMP | Yes | No | No | No |
| HLS | No | Yes | Yes | Yes |
| WebRTC | Yes | Yes | Yes | Yes |
| DASH | No | Yes | Limited | Yes |

---

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
1. Always validate stream keys before allowing publishing
2. Implement adaptive bitrate for varying network conditions
3. Use CDN for global playback distribution
4. Monitor stream health and viewer metrics
5. Record all streams for VOD and compliance
6. Implement failover for critical streams

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
