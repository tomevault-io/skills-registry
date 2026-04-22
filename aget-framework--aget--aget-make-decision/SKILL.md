---
name: aget-make-decision
description: Make decisions with documented rationale Use when this capability is needed.
metadata:
  author: aget-framework
---

# aget-make-decision

Make decisions with documented rationale, authority verification, and communication plan. Creates decision records for audit trail.

## Instructions

When this skill is invoked:

1. **Verify Authority**
   - Check if decision is within authority scope
   - Identify if escalation needed
   - Note delegation if applicable

2. **Analyze Options**
   - Document options considered
   - Evaluate pros/cons
   - Assess risks per option

3. **Make Decision**
   - Select option
   - Document rationale
   - Note trade-offs accepted

4. **Plan Communication**
   - Identify stakeholders
   - Determine communication method
   - Draft key messages

5. **Create Decision Record**
   - ADR-style documentation
   - Enable future reference
   - Support audit requirements

## Output Format

```markdown
# Decision Record: [Title]

**Date**: [YYYY-MM-DD]
**Decision Maker**: [Agent/Role]
**Status**: [Proposed / Accepted / Superseded]

---

## Context

[What situation requires this decision]

---

## Decision

**We will**: [Clear statement of decision]

---

## Authority

| Check | Status |
|-------|--------|
| Within scope | [Yes/No/Escalate] |
| Delegation | [N/A or source] |

---

## Options Considered

| Option | Pros | Cons | Risk |
|--------|------|------|------|
| [Option 1] | [+] | [-] | [Risk] |
| [Option 2] | [+] | [-] | [Risk] |
| **Selected** | | | |

---

## Rationale

[Why this option was chosen over alternatives]

---

## Trade-offs Accepted

- [What we're giving up]
- [What we're gaining]

---

## Communication Plan

| Stakeholder | Method | Timing | Key Message |
|-------------|--------|--------|-------------|
| [Who] | [Email/Meeting/etc.] | [When] | [What to convey] |

---

## Consequences

- [Expected outcome 1]
- [Potential risk to monitor]
```

## Constraints

- **C1**: NEVER make decisions exceeding authority scope — authority boundaries must be respected
- **C2**: NEVER skip rationale documentation — decisions need audit trail
- **C3**: NEVER communicate decisions without stakeholder identification — wrong audience creates confusion

## Related

- SKILL-025: aget-make-decision specification
- ONTOLOGY_executive.yaml: Decision, Authority, Communication concepts
- CAP-EXE-001: Decision Making capability

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/aget-framework) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
