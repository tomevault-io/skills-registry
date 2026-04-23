---
name: web-search-strategy
description: Conduct structured research with cross-referenced findings and confidence scoring Use when this capability is needed.
metadata:
  author: cdalsoniii
---

# Web Search Strategy Skill

Conduct structured research with cross-referenced findings and confidence scoring.

## Trigger Conditions
- Research request for technology evaluation
- Need to validate technical decisions against current best practices
- User invokes with "research" or "technology evaluation"

## Input Contract
- **Required:** Research question or topic
- **Optional:** Constraints (technology stack, budget, timeline)

## Output Contract
- Ranked findings with confidence levels (high/medium/low)
- Cross-referenced sources (minimum 2 per finding)
- Recommendation with rationale

## Tool Permissions
- **Read:** Project context for relevance filtering
- **Web:** Search and fetch capabilities
- **Write:** Research reports

## Execution Steps
1. Decompose research question into specific search queries
2. Execute searches across multiple authoritative sources
3. Cross-reference findings (minimum 2 sources per claim)
4. Assign confidence levels based on source authority and agreement
5. Synthesize findings into structured report
6. Provide recommendation with caveats

## Success Criteria
- Every finding cross-referenced from 2+ sources
- Confidence levels assigned to all claims
- Recommendation includes trade-offs and caveats
- Sources are authoritative and current (within 1 year)

## Escalation Rules
- Escalate if findings are contradictory across authoritative sources
- Escalate if confidence is Low for a critical decision input

## Example Invocations

**Input:** "Compare Temporal vs Restate for workflow orchestration in Go"

**Output:** Research report: Temporal (mature, large community, complex deployment) vs Restate (simpler deployment, newer, smaller community). Confidence: HIGH on feature comparison, MEDIUM on production readiness (Restate is newer). Recommendation: Temporal for production workloads requiring proven reliability; Restate for simpler use cases valuing operational simplicity.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/cdalsoniii) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
