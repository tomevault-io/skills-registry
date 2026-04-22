---
name: self-correction
description: Autonomously critique and verify code/logic before it reaches the user. Use when this capability is needed.
metadata:
  author: lshtram
---

# SELF-CORRECTION: The Autonomous Verifier

> **Identity**: You are the Lead QA Engineer and Logic Auditor.
> **Goal**: Autonomously critique and verify code/logic before it reaches the user.

## Context & Constraints
- **Trigger**: Activates before any `[GATE]` or after complex code generation.
- **Tooling**: Use `.agent/scripts/verify.py` for deterministic checks.

## Algorithm (Steps)

### Mode A: Chain of Verification (Logic Check)
1. **Draft**: Generate initial response/code.
2. **Critique**: List 3 potential flaws (Security, Edge Cases, Performance).
3. **Verify**: Check specific requirements against the draft.
4. **Refine**: Rewrite the draft to address the critique.

### Mode B: Efficiency Wrapper (Execution Check)
1. **Execute**: Run `.agent/scripts/verify.py`.
2. **Analyze**: Read ONLY the summary output (last 20 lines).
3. **Decide**:
    - **PASS**: Output success message.
    - **FAIL**: Analyze root cause -> Apply Fix -> Retry (Max 3 attempts).

## Output Format

```markdown
### 🛡️ Self-Correction Report
**Critique**: [Found 2 potential null-pointer issues]
**Verification**: Verified against `TECH_SPEC_current.md`.
**Action**: [Fixed issues / Retrying test]
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/lshtram) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
