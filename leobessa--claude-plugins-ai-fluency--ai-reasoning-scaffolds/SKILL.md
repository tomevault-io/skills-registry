---
name: ai-reasoning-scaffolds
description: Use AI as a thinking partner by injecting checklists, reasoning trees, critique loops, and stepwise logic. Delegate sub-reasoning without losing coherence. Use when this capability is needed.
metadata:
  author: leobessa
---

# Overview

**AI Reasoning Scaffolds** is Layer 4 of AI fluency—the ability to structure AI's reasoning process rather than just its output. This transforms AI from a generator into a thinking partner.

**Core Principle:** AI follows the reasoning structure you provide. No scaffold = no reliable reasoning.

**Fluency Signal:** AI outputs show internal coherence and traceable logic.

---

## When to Use This Skill

- Complex analysis requiring multiple considerations
- Decision-making with trade-offs
- Problems requiring systematic evaluation
- When AI output lacks depth or misses important factors
- When you need to understand AI's reasoning path

---

## Scaffold Types

### 1. Checklists

**Purpose:** Ensure systematic coverage of required elements.

**Pattern:**
```markdown
Before providing your analysis, work through this checklist:

□ Identify the main claim
□ List supporting evidence
□ Identify gaps in evidence
□ Consider counter-arguments
□ Assess source credibility
□ Note any unstated assumptions
□ Rate confidence level (High/Medium/Low)

Then synthesize your findings into a response.
```

**When to use:**
- Quality assurance tasks
- Review processes
- Comprehensive analysis
- Compliance checking

### 2. Reasoning Trees

**Purpose:** Guide systematic exploration of options.

**Pattern:**
```markdown
Analyze this problem using the following decision tree:

1. First, classify the problem type:
   - If performance issue → Go to Branch A
   - If feature request → Go to Branch B
   - If unclear → Ask clarifying question

Branch A (Performance):
  2. Identify the bottleneck layer:
     - Database → Check query optimization
     - Application → Check algorithm complexity
     - Network → Check payload size
  3. For each identified issue, propose solutions ranked by effort/impact

Branch B (Feature):
  2. Assess alignment with roadmap...
  [continue structure]
```

**When to use:**
- Diagnostic problems
- Classification tasks
- Decision processes
- Troubleshooting

### 3. Stepwise Reasoning

**Purpose:** Force explicit thinking through steps.

**Pattern:**
```markdown
Solve this problem step by step. Show your work for each step:

Step 1: State the problem in your own words
[Your response]

Step 2: Identify what information is given
[Your response]

Step 3: Identify what information is needed
[Your response]

Step 4: Determine the approach
[Your response]

Step 5: Execute the approach
[Your response]

Step 6: Verify the result
[Your response]

Step 7: State the conclusion
[Your response]
```

**When to use:**
- Mathematical or logical problems
- Complex analysis
- Debugging
- Audit trails needed

### 4. Critique Loops

**Purpose:** Generate opposing perspectives.

**Pattern:**
```markdown
Analyze this proposal using a structured critique:

ROUND 1 - ADVOCATE:
Present the strongest case FOR this proposal. What are the benefits?

ROUND 2 - CRITIC:
Now argue AGAINST the proposal. What are the risks and weaknesses?

ROUND 3 - SYNTHESIS:
Reconcile these perspectives. What's the balanced view? What conditions would make this proposal succeed or fail?
```

**When to use:**
- Evaluating options
- Risk assessment
- Stress-testing ideas
- Avoiding confirmation bias

### 5. Multi-Perspective Analysis

**Purpose:** Examine from multiple stakeholder viewpoints.

**Pattern:**
```markdown
Analyze this decision from multiple perspectives:

PERSPECTIVE 1 - Customer:
- What do they gain?
- What concerns would they have?

PERSPECTIVE 2 - Engineering:
- What's the implementation complexity?
- What are the technical risks?

PERSPECTIVE 3 - Business:
- What's the revenue impact?
- What's the competitive implication?

PERSPECTIVE 4 - Operations:
- How does this affect support load?
- What's the maintenance burden?

SYNTHESIS:
Which perspective carries the most weight for this decision? What trade-offs are acceptable?
```

**When to use:**
- Strategic decisions
- Product decisions
- Organizational changes
- Trade-off analysis

---

## Scaffold Design Principles

### Principle 1: Explicit > Implicit

Don't assume AI will consider something—make it explicit:

**Weak:**
> "Consider all relevant factors"

**Strong:**
> "Consider these factors: cost, timeline, risk, team capacity, dependencies"

### Principle 2: Ordered > Unordered

Sequence matters for reasoning quality:

**Weak:**
> "Think about benefits, risks, costs, and feasibility"

**Strong:**
> "First identify benefits, then list risks for each benefit, then estimate costs, then assess overall feasibility"

### Principle 3: Constrained > Open

Bounded options produce better reasoning:

**Weak:**
> "Rate how confident you are"

**Strong:**
> "Rate confidence as: High (>80% sure), Medium (50-80%), Low (<50%). State the main uncertainty."

### Principle 4: Observable > Hidden

Request visible reasoning:

**Weak:**
> "Give me your best answer"

**Strong:**
> "Show your reasoning process, then give your conclusion. I need to understand how you arrived at it."

---

## Delegation Patterns

### Delegate Sub-Reasoning

For complex problems, delegate specific reasoning tasks:

```markdown
Main problem: [Complex question]

I'll work through this systematically. For each sub-question, provide your reasoning:

Sub-question 1: [Bounded aspect of the problem]
[AI responds with focused analysis]

Sub-question 2: [Another aspect]
[AI responds]

Now I'll synthesize these inputs into my decision.
```

### Chain Reasoning Across Turns

```markdown
Turn 1: "Analyze the current state of X"
[AI provides analysis]

Turn 2: "Given your analysis, what are the top 3 strategic options?"
[AI generates options building on prior analysis]

Turn 3: "For option 2, do a deeper risk analysis"
[AI drills into specific option]

Turn 4: "Synthesize our conversation into a recommendation"
[AI produces coherent output from the reasoning chain]
```

---

## Practices

### Chain-of-Reasoning Prompting

Add reasoning scaffolds to any prompt:

```markdown
[Your original prompt]

Think through this step by step:
1. First, understand what's being asked
2. Identify the key factors that matter
3. Consider the implications of each factor
4. Weigh trade-offs if any exist
5. Form your conclusion
6. Verify your reasoning makes sense

Show your work for steps 1-5, then provide your final answer.
```

### Counter-Argument Generation

Build in skepticism:

```markdown
[Your original prompt]

After your initial response, challenge it:
- What's the strongest argument against your conclusion?
- What assumption, if wrong, would invalidate your reasoning?
- What information would make you change your answer?
```

### Self-Critique Prompting

```markdown
[Your original prompt]

After your response:
SELF-CRITIQUE:
- What did you do well?
- What might be wrong or incomplete?
- What would you do differently with more time?
- Confidence level: High/Medium/Low and why
```

---

## Assessment Criteria

**Layer 4 Complete When:**
- [ ] Regularly uses structured reasoning scaffolds
- [ ] AI outputs show explicit reasoning steps
- [ ] Can delegate sub-reasoning without losing coherence
- [ ] Uses critique loops to stress-test AI conclusions
- [ ] Has created reusable reasoning templates

---

## Common Scaffold Failures

### Failure 1: No Scaffold for Complex Tasks

**Wrong:** "Analyze this business problem"
**Right:** "Analyze this problem using: 1) Problem definition 2) Root cause analysis 3) Option generation 4) Option evaluation 5) Recommendation"

### Failure 2: Scaffold Too Loose

**Wrong:** "Think about pros and cons"
**Right:** "List exactly 3 pros and 3 cons, ranked by importance, with one sentence explanation each"

### Failure 3: Missing Verification Step

**Wrong:** "Solve this calculation"
**Right:** "Solve this calculation, then verify by working backward from your answer"

### Failure 4: Reasoning Without Output

**Wrong:** "Think through this carefully"
**Right:** "Think through this carefully and show your reasoning at each step"

---

## Related Skills

- [ai-instruction-design](../ai-instruction-design/SKILL.md) — Format for scaffolds
- [ai-evaluation-verification](../ai-evaluation-verification/SKILL.md) — Verifying scaffold outputs
- [ai-problem-framing](../ai-problem-framing/SKILL.md) — Decomposition for delegation

---

## Learn More

- [Scaffold Templates](references/scaffold-templates.md)
- [Critique Loop Examples](references/critique-examples.md)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/leobessa) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
