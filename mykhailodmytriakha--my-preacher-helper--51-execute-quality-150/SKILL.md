---
name: 51-execute-quality-150
description: [51] EXECUTE. Commitment to maximum quality work with 150% coverage. Use when you need the highest quality output for critical tasks, complex problems, important decisions, or when standard work isn't enough. Triggers on \"maximum quality\", \"150% mode\", \"full quality\", \"critical task\", or when you explicitly want AI to work at its best. Use when this capability is needed.
metadata:
  author: mykhailodmytriakha
---

# Execute-Quality 150 Protocol

**Core Principle:** Commit to excellence. Work at 150% — 100% core delivery + 50% strengthening through verification, alternatives, and risk awareness.

## What This Skill Does

When you invoke this skill, you're asking AI to:
- **Work deeper** — Full context, not fragments
- **Think harder** — Verify assumptions, consider alternatives
- **Validate more** — Check at every step, not just at the end
- **Deliver better** — With confidence levels and documented reasoning

## The 150% Quality Standard

| Dimension | 100% Core | +50% Strengthening |
|-----------|-----------|-------------------|
| **Context** | Understand the task | + Understand dependencies and boundaries |
| **Research** | Find answers | + Verify from multiple sources |
| **Assumptions** | Use them | + Test and validate them |
| **Solutions** | Provide one | + Consider alternatives |
| **Risks** | Ignore | + Identify and mitigate |
| **Confidence** | Implicit | + Explicit with percentages |

## Execution Protocol

### Step 1: ASSESS
Evaluate what maximum quality means for THIS task:
- What's the complexity level?
- What context needs full reading?
- What assumptions need checking?
- What could go wrong?

### Step 2: COMMIT
Declare your quality approach:
```
🎯 **Max-Quality 150 Engaged**

For this task, maximum quality means:
- [What I'll read fully]
- [What I'll verify]
- [What alternatives I'll consider]
- [What risks I'll check]
```

### Step 3: EXECUTE
Work with 150% coverage:
- **Read fully** — Complete files, not snippets
- **Verify assumptions** — Don't trust, confirm
- **Consider alternatives** — At least 3 approaches
- **Check risks** — What could fail?
- **Validate continuously** — Not just at the end

### Step 4: DELIVER
Provide result with quality indicators:
- Confidence level (percentage)
- What was verified vs assumed
- Known limitations
- Recommendations

## Quality Behaviors

When max-quality-150 is active:

### Reading & Research
```
❌ Standard: Read first 50 lines of file
✅ Max-Quality: Read entire file + imports + dependencies
```

### Assumptions
```
❌ Standard: "This function probably handles X"
✅ Max-Quality: "Verified: function handles X (line 45-67)"
```

### Solutions
```
❌ Standard: "Here's the fix"
✅ Max-Quality: "Considered 3 approaches, recommending A because..."
```

### Validation
```
❌ Standard: Check if it compiles
✅ Max-Quality: Test edge cases, verify integration, check performance
```

## Output Format

When delivering with max-quality-150:

```
🎯 **Max-Quality 150 Delivery**

**Task:** [What was requested]

**Quality Approach Used:**
- ✅ Full context read: [files/systems]
- ✅ Assumptions verified: [list]
- ✅ Alternatives considered: [count]
- ✅ Risks assessed: [count]

**Result:**
[Detailed, high-quality output]

**Confidence:** [X]%
**Verified:** [What was confirmed]
**Assumed:** [What remains unverified]
**Limitations:** [Known gaps]
```

## When to Use This Skill

Invoke max-quality-150 for:
- **Critical tasks** — Production deployments, security changes
- **Complex problems** — Multi-system, many dependencies
- **Important decisions** — Architecture, strategy, major choices
- **When stakes are high** — User data, business logic, integrations
- **When you need confidence** — Can't afford to be wrong

## Operational Rules

1. **FULL CONTEXT:** Read complete files, not fragments
2. **VERIFY ASSUMPTIONS:** Don't trust, confirm with evidence
3. **MULTIPLE PERSPECTIVES:** Consider at least 3 approaches
4. **CONTINUOUS VALIDATION:** Check quality at every step
5. **EXPLICIT CONFIDENCE:** State certainty levels numerically
6. **DOCUMENT REASONING:** Explain why, not just what
7. **ACKNOWLEDGE LIMITS:** State what you don't know

## Examples

### ❌ Standard Quality
```
User: "Review this authentication code"
AI: "The code looks fine. Consider adding input validation."
```

### ✅ Max-Quality 150
```
User: "Review this authentication code"
AI: "🎯 Max-Quality 150 Engaged

For this security-critical review, I'll:
- Read full auth module + dependencies
- Check against OWASP standards
- Verify session handling
- Test edge cases mentally

[After thorough review]

🎯 Max-Quality 150 Delivery

Confidence: 92%

Verified Issues:
1. Password not hashed (line 45) - Critical
2. No rate limiting (lines 78-92) - High
3. Session token predictable (line 112) - Critical

Verified Safe:
- Input sanitization present (line 23)
- HTTPS enforced (config verified)

Alternatives Considered:
- bcrypt vs argon2 for hashing
- Token-based vs session-based auth

Risks Identified:
- Current code vulnerable to brute force
- Session hijacking possible

Recommendations: [detailed fixes]"
```

## Failure Modes & Recovery

| Failure | Detection | Recovery |
|---------|-----------|----------|
| **Shallow work** | Skipping full context | Stop, read completely |
| **Unverified claims** | Assumptions without evidence | Go verify before proceeding |
| **Single solution** | No alternatives considered | Generate 3+ options |
| **Missing risks** | No failure analysis | Do risk assessment |
| **Low confidence** | <85% certainty | More research or flag to user |

## Relationship to Other Skills

- **deep-think-150** — For quality reasoning (thinking)
- **max-quality-150** — For quality execution (doing)

Use both when task requires excellent thinking AND excellent execution.

## Session Log Entry (MANDATORY)

After completing this skill, write to `.sessions/SESSION_[date]-[name].md`:

```
### [date - HH"MM] Execute-Quality 150 Complete
**Task:** <task executed>
**Quality Score:** <confidence %>
**Verified:** <what was verified>
**Artifacts:** <files delivered>
```

---

**Remember:** max-quality-150 is not about being slow — it's about being thorough. The extra effort upfront prevents costly mistakes later. Quality is an investment, not an expense.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mykhailodmytriakha) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
