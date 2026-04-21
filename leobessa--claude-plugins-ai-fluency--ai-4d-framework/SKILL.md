---
name: ai-4d-framework
description: Apply Anthropic's 4D Framework for AI delegation: Delegation (task selection), Description (instructions), Discernment (verification), and Diligence (iteration). Use when this capability is needed.
metadata:
  author: leobessa
---

# Overview

The **4D Framework** is Anthropic's model for effective AI delegation, organizing AI fluency into four interconnected components. Each dimension addresses a critical aspect of working with AI systems.

**Core Principle:** Effective AI use requires competence across all four dimensions—weakness in any dimension limits overall effectiveness.

---

## When to Use This Skill

- Assessing overall AI fluency
- Structuring AI training programs
- Diagnosing AI effectiveness gaps
- Building systematic AI practices
- Teaching others effective AI use

---

## The Four Dimensions

### Dimension 1: Delegation

**Question:** What tasks should I give to AI?

**Core competency:** Selecting appropriate tasks for AI based on realistic assessment of capabilities and limitations.

**Key elements:**
- Understanding AI strengths and weaknesses
- Matching task characteristics to AI capabilities
- Recognizing when AI is inappropriate
- Decomposing complex tasks for hybrid human-AI execution

**Good delegation:**
- Tasks within AI's demonstrated capabilities
- Clear success criteria exist
- Output can be verified
- Risk of error is acceptable

**Poor delegation:**
- Tasks requiring real-time information
- Decisions requiring accountability
- Tasks you can't verify
- High-stakes irreversible actions

**Related layers:** Layer 0 (Cognitive Readiness), Layer 1 (System Literacy)

---

### Dimension 2: Description

**Question:** How do I specify what I want?

**Core competency:** Crafting clear, complete instructions that produce reliable, high-quality outputs.

**Key elements:**
- Role definition (who AI should act as)
- Scope bounding (what's in/out of bounds)
- Format specification (output structure)
- Decision rules (how to handle judgment calls)
- Abstraction level (detail and expertise level)

**Good description:**
```markdown
As a senior technical writer, review this API documentation for:
1. Accuracy of code examples (test each one)
2. Completeness of parameter descriptions
3. Clarity for developers new to this API

Format: For each issue found, provide:
- Location (section/line)
- Issue type
- Current text
- Suggested revision
- Priority (High/Medium/Low)

If you're unsure whether something is an issue, include it with a "Possible" tag.
```

**Poor description:**
```markdown
Review this documentation and let me know what you think.
```

**Related layers:** Layer 2 (Problem Framing), Layer 3 (Instruction Design)

---

### Dimension 3: Discernment

**Question:** How do I evaluate AI output?

**Core competency:** Critically assessing AI outputs for accuracy, completeness, and fitness for purpose.

**Key elements:**
- Verification against sources
- Logic and reasoning checks
- Completeness assessment
- Bias and error detection
- Confidence calibration

**Discernment practices:**

```markdown
VERIFICATION PROTOCOL

1. LOGIC CHECK
   □ Do conclusions follow from premises?
   □ Are there reasoning gaps?
   □ Is the argument circular?

2. FACT CHECK
   □ Verify 3+ specific claims against sources
   □ Check citations actually exist
   □ Validate quantitative claims

3. COMPLETENESS CHECK
   □ Are all requested elements present?
   □ What's notably absent?
   □ Ask AI: "What did you NOT include?"

4. CONFIDENCE ASSESSMENT
   □ What's the confidence level?
   □ Where is AI most/least certain?
   □ What would change the assessment?
```

**Related layers:** Layer 4 (Reasoning Scaffolds), Layer 5 (Evaluation & Verification)

---

### Dimension 4: Diligence

**Question:** How do I systematically improve?

**Core competency:** Iterating on AI interactions and building improving systems over time.

**Key elements:**
- Systematic iteration on outputs
- Capturing learnings
- Building reusable patterns
- Workflow integration
- Continuous improvement

**Diligence practices:**

```markdown
ITERATION PROTOCOL

1. ASSESS OUTPUT
   - What's working?
   - What needs improvement?
   - What's the specific gap?

2. DIAGNOSE CAUSE
   - Is it a delegation issue?
   - Is it a description issue?
   - Is it an AI limitation?

3. REFINE APPROACH
   - What specific change will address the gap?
   - Test one change at a time
   - Document what you learn

4. CAPTURE PATTERN
   - If this worked, document why
   - Create reusable template
   - Share with others
```

**Related layers:** Layer 6 (Workflow Integration), Layer 7 (System Governance), Layer 8 (Strategic Fluency)

---

## 4D Assessment Framework

### Self-Assessment

Rate each dimension (1-5):

```markdown
4D SELF-ASSESSMENT

DELEGATION (Task Selection)
1 - Struggle to identify appropriate AI tasks
2 - Sometimes pick tasks AI handles poorly
3 - Generally good task selection
4 - Consistently good task-capability matching
5 - Expert at decomposing complex tasks for AI

Score: ___

DESCRIPTION (Instructions)
1 - Prompts are vague, results inconsistent
2 - Basic structure but missing elements
3 - Good prompts with role, scope, format
4 - Consistently well-structured prompts
5 - Prompts are reusable specifications

Score: ___

DISCERNMENT (Verification)
1 - Accept AI output without verification
2 - Occasional spot checks
3 - Regular verification of key claims
4 - Systematic verification protocol
5 - Comprehensive multi-gate verification

Score: ___

DILIGENCE (Improvement)
1 - Same approach regardless of results
2 - Occasional iteration when problems obvious
3 - Regular iteration and improvement
4 - Systematic capture of learnings
5 - Documented workflows with metrics

Score: ___

TOTAL: ___ / 20

Interpretation:
4-8:   Beginner - Focus on fundamentals
9-12:  Developing - Build systematic practices
13-16: Proficient - Refine and specialize
17-20: Expert - Share and scale
```

### Gap Analysis

When AI isn't working well:

```markdown
4D GAP ANALYSIS

Symptom: [What's going wrong]

DELEGATION CHECK:
□ Was this an appropriate task for AI?
□ Should it have been decomposed differently?
□ Did I overestimate AI capability?
Gap found: [Yes/No] Details: ___

DESCRIPTION CHECK:
□ Were instructions clear and complete?
□ Was the format specified?
□ Were decision rules explicit?
Gap found: [Yes/No] Details: ___

DISCERNMENT CHECK:
□ Did I verify appropriately?
□ What did I miss?
□ Was my confidence calibrated?
Gap found: [Yes/No] Details: ___

DILIGENCE CHECK:
□ Did I iterate effectively?
□ Did I capture learnings?
□ Is there a pattern to improve?
Gap found: [Yes/No] Details: ___

Primary gap: _______________
Remediation: _______________
```

---

## Dimension Interactions

### How Dimensions Compound

```
Strong Delegation + Weak Description = Right task, wrong execution
Strong Description + Weak Discernment = Good output, unverified errors
Strong Discernment + Weak Diligence = Catches errors, doesn't improve
Strong Diligence + Weak Delegation = Improving at wrong tasks
```

### Development Sequence

**Recommended progression:**

1. **Start with Discernment** - Learn to evaluate output before trusting it
2. **Build Description** - Learn to get better output to evaluate
3. **Develop Delegation** - Learn what AI can/cannot do well
4. **Add Diligence** - Build systems that improve over time

---

## Practices

### 4D Daily Check

```markdown
TODAY'S AI INTERACTIONS

Task 1: [Description]
- Delegation: Was this appropriate? [Y/N]
- Description: Were instructions clear? [Y/N]
- Discernment: Did I verify adequately? [Y/N]
- Diligence: What did I learn? [Notes]

Task 2: [Description]
...

Pattern to improve: [What I'll do differently]
```

### 4D Prompt Review

Before running important prompts:

```markdown
4D PROMPT CHECK

□ DELEGATION: Is this task appropriate for AI?
□ DESCRIPTION: Are instructions complete (role, scope, format, rules)?
□ DISCERNMENT: How will I verify the output?
□ DILIGENCE: How will I capture what I learn?
```

---

## Assessment Criteria

**4D Framework Mastery When:**
- [ ] Can assess own AI use across all four dimensions
- [ ] Diagnoses problems by identifying which dimension is weak
- [ ] Has systematic practices for each dimension
- [ ] Dimensions work together fluidly
- [ ] Can teach framework to others

---

## Related Skills

Each dimension maps to AI Fluency layers:

| Dimension | Primary Layers | Key Skills |
|-----------|---------------|------------|
| Delegation | 0, 1 | ai-cognitive-readiness, ai-system-literacy |
| Description | 2, 3 | ai-problem-framing, ai-instruction-design |
| Discernment | 4, 5 | ai-reasoning-scaffolds, ai-evaluation-verification |
| Diligence | 6, 7, 8 | ai-workflow-integration, ai-system-governance, ai-strategic-fluency |

---

## Learn More

- [4D Assessment Templates](references/4d-templates.md)
- [Dimension Development Exercises](references/4d-exercises.md)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/leobessa) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
