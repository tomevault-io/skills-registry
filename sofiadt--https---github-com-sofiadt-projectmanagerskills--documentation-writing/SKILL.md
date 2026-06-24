---
name: documentation-writing
description: When the user wants to write or improve project documentation. Use when the user says "I need to write specs," "runbook," "standard operating procedure," "process documentation," "I need to document this," "project docs," "how-to guide," or "technical documentation." For writing project briefs, see project-proposals or project-kickoff. Use when this capability is needed.
metadata:
  author: SofiaDT
---

# Documentation Writing

You are an expert project manager. Your goal is to help the user write clear, well-structured project documentation that teams can actually use.

---

## Documentation Types & Templates

### Requirements Document

```markdown
## Requirements Document — [Project Name]

**Version**: 1.0 | **Date**: [Date] | **Author**: [Name]

### Purpose
[One paragraph on why this doc exists]

### Functional Requirements
Each requirement should have an ID, description, and acceptance criteria.

| ID | Requirement | Acceptance Criteria |
|----|-------------|-------------------|
| FR-001 | [Functional requirement description] | [ ] User can [action], [ ] System returns [result] |
| FR-002 | | |

### Non-Functional Requirements
| ID | Requirement | Target |
|----|-------------|--------|
| NFR-001 | Performance | Page loads in <2s |
| NFR-002 | Availability | 99.9% uptime |

### Constraints & Assumptions
- [Constraint 1]
- [Assumption 1]
```

---

### Standard Operating Procedure (SOP)

```markdown
## SOP: [Process Name]

**Version**: 1.0 | **Last Updated**: [Date] | **Owner**: [Name]

### Purpose
[What is this process? When do we use it?]

### Scope
[Who does this? When? Any exceptions?]

### Prerequisites
- [Pre-condition 1]
- [Before you start, you need X]

### Step-by-Step Process

1. **[Step 1 - Title]**
   - Do X
   - Check for Y
   - If Z, then go to step 4

2. **[Step 2 - Title]**
   - Detailed instructions

### Decision Tree (if applicable)
- If [condition], then [action]
- If [other condition], then [other action]

### Tools & Systems Used
- [Tool 1]: [What you use it for]
- [System 1]: [How to access]

### Common Issues & Troubleshooting
| Issue | Solution |
|-------|----------|
| [Problem] | [Fix] |

### Escalation
If [warning sign], escalate to [person/team] by [method].

### Related Documents
- [Link to related SOP or process]

### Sign-Off
- Author: [Sign, Date]
- Reviewer: [Sign, Date]
```

---

### Runbook (Operational)

```markdown
## Runbook: [Process Name]

**Audience**: [Operations team / developers / support]
**Emergency? Call [Contact]**

### Quick Reference
[Key info if something is broken]

### Before You Start
- [ ] Check that [system] is accessible
- [ ] Have [tool/credentials] handy
- [ ] Notify [team] that you're starting

### Normal Run (Happy Path)
1. [Step 1]
2. [Step 2]

### If Something Goes Wrong
[Emergency troubleshooting]

### After You're Done
- [ ] Verify [outcome]
- [ ] Log results in [system]
- [ ] Update status dashboard
```

---

### Playbook (Scenario-Based)

```markdown
## Playbook: [Scenario Name]

**When to use**: [Describe the scenario]

### Goal
[What should happen by the end of this playbook]

### Players
| Role | Name | Contact |
|------|------|---------|
| Lead | [Name] | [Contact] |

### Steps
1. [Action]
2. [Action]

### Decision Points
- If [condition], follow Path A
- If [condition], follow Path B

### Success Criteria
- [ ] [Outcome 1 achieved]
- [ ] [Outcome 2 achieved]
```

---

## Documentation Best Practices

| Principle | Why | How |
|-----------|-----|-----|
| **Audience first** | Different people need different docs | Tailor language / detail for the reader |
| **Up to date** | Outdated docs are worse than no docs | Assign an owner, review cadence |
| **Version control** | Track changes | V1.0, V1.1, V2.0 with date |
| **Simple language** | Clarity > cleverness | Short sentences, active voice |
| **Examples** | Show, don't tell | Include screenshots, sample outputs |
| **Indexable** | Easy to find | Use headers, TOC, consistent naming |
| **Centralized** | One source of truth | Store in shared location (Wiki, Confluence, Notion) |

---

## Related Skills

- `project-proposals` — requires clear written scopes
- `project-kickoff` — project brief is key documentation
- `sprint-planning` — sprint docs (acceptance criteria) need clarity
- `requirements-gathering` — requirements become functional specs

---
> Source: [SofiaDT/https---github.com-SofiaDT-projectmanagerskills](https://github.com/SofiaDT/https---github.com-SofiaDT-projectmanagerskills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
