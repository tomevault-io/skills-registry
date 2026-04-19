---
name: stage-and-risk-selection
description: Select which prompting rules to apply based on the requirements-engineering task stage (Discovery, Specification, Validation, Incident, Audit/Compliance) and risk. Use when writing prompts/guidelines so you don’t misapply strict rules (e.g., RFC-2119, ban vague words, “no solutions”) in the wrong context. Use when this capability is needed.
metadata:
  author: ezej
---

# Stage And Risk Selection

## Workflow

1. Pick exactly one **stage**:
   - **Discovery**: explore goals/values/unknowns; avoid fake precision.
   - **Specification**: write contract-like requirements; enforce atomic + testable.
   - **Validation**: check satisfiability, consistency, gaps, and evidence.
   - **Incident**: time-critical mitigation + learning; label uncertainty and act.
   - **Audit/Compliance**: focus on prohibitions, access control, and loopholes.
2. Declare **risk level** (Low/Med/High). If **High**, require explicit uncertainty handling and a targeted self-audit.
3. Choose the **artifact** you want:
   - Clarifying questions, stakeholder summary, constraints list, requirement set, acceptance criteria, conflict matrix, mitigation plan, etc.
4. Add stage-appropriate guardrails:
   - **Discovery**: allow qualitative terms, require operationalization (examples/rubric/study/TODO metric plan).
   - **Specification**: use RFC-2119 modality in Statements; include fit criteria/validation; forbid inventing facts.
   - **Incident**: limit questions; provide an actionable plan with confidence labels; interleave need/option/risk.
   - **Audit/Compliance**: keep prohibitions as SHALL NOT; run a loophole scan.
5. Add a stop rule:
   - “If information is missing, write `UNKNOWN (needs follow-up)` and list the minimum follow-up questions.”

## Prompt snippet (copy/paste)

```text
Stage: <Discovery|Specification|Validation|Incident|Audit/Compliance>
Risk: <Low|Med|High>

Role: Senior requirements engineer.
Rules:
- Do not invent facts; mark missing info as UNKNOWN (needs follow-up).
- Apply stage-appropriate guardrails.

Output: <describe artifact + required schema>
Inputs: <paste the full input text here>
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ezej) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
