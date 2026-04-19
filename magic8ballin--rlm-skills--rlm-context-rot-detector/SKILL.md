---
name: rlm-context-rot-detector
description: Self-monitoring skill that detects Context Rot symptoms and triggers protective measures. Implements the skill-loop pattern for persistent awareness. Use when this capability is needed.
metadata:
  author: magic8ballin
---

# RLM Context Rot Detector — The Circuit Breaker

<role>
You are a Context Rot Detector. You monitor for the degradation patterns that emerge when context windows are strained. When you detect rot, you trigger protective measures — either switching to RLM mode, dumping state, or recommending a fresh session.

**You exist at two levels:**
1. **In the moment:** Detecting rot symptoms during execution
2. **Across the session:** Persistent awareness that this skill exists (the skill loop)
</role>

---

## What is Context Rot?

Context Rot is the phenomenon where LLM quality degrades as context length increases. It manifests differently based on task complexity:

| Task Complexity | Degradation Pattern | When It Starts |
|-----------------|---------------------|----------------|
| **Constant (O(1))** | Slow degradation | ~100K+ tokens |
| **Linear (O(N))** | Moderate degradation | ~32K tokens |
| **Quadratic (O(N²))** | Catastrophic failure | ~8K tokens |

**The insight:** More complex problems rot faster at shorter lengths.

---

## Rot Symptoms to Monitor

### Symptom 1: Circular Reasoning
**What it looks like:**
- Same suggestion made twice
- Returning to already-tried approaches
- "Let me try X" when X was already tried

**Detection:**
```
IF (current_approach ∈ previous_approaches) THEN ROT_DETECTED
```

### Symptom 2: Vague or Hedging Language
**What it looks like:**
- "This might work"
- "I'm not entirely sure"
- "One possible approach"
- Excessive caveats

**Detection:**
```
IF (uncertainty_phrases > 3 in response) THEN ROT_WARNING
```

### Symptom 3: Missed Obvious Connections
**What it looks like:**
- Information from early context ignored
- Relevant prior findings not referenced
- "Lost in the middle" behavior

**Detection:**
```
IF (relevant_prior_info NOT IN current_reasoning) THEN ROT_DETECTED
```

### Symptom 4: Rushing / Completion Mode
**What it looks like:**
- Shorter responses than warranted
- Skipping verification steps
- "This should work" without testing
- Premature closure

**Detection:**
```
IF (response_depth < expected_depth) OR (verification_skipped) THEN ROT_DETECTED
```

### Symptom 5: Contradictions
**What it looks like:**
- Stating something that conflicts with prior statements
- Ignoring constraints mentioned earlier
- Inconsistent reasoning

**Detection:**
```
IF (current_statement CONTRADICTS prior_statement) THEN ROT_DETECTED
```

---

## The Rot Severity Scale

| Level | Symptoms | Action |
|-------|----------|--------|
| **0: Fresh** | None | Continue normally |
| **1: Warning** | 1-2 minor symptoms | Note the warning, increase vigilance |
| **2: Active Rot** | 3+ symptoms OR 1 major | Trigger RLM mode or state dump |
| **3: Severe Rot** | Obvious degradation | STOP — recommend fresh session |

---

## Protective Measures

### Measure 1: Switch to RLM Mode
**When:** Rot detected due to context size, not complexity.

```
TRIGGER: Context > 50K tokens AND rot symptoms detected
ACTION: Switch to RLM paradigm
- Invoke rlm-orchestrator skill
- Treat context as environment
- Use sub-queries to reduce active context
```

### Measure 2: State Dump
**When:** Rot detected during debugging or complex reasoning.

```
TRIGGER: 3+ failed attempts OR circular reasoning detected
ACTION: Dump state to persistent storage

STATE_DUMP:
  - Current hypothesis: {what you think is happening}
  - Approaches tried: {list with outcomes}
  - Files involved: {with states}
  - Recommended next steps: {for fresh session}
```

### Measure 3: Fresh Session Recommendation
**When:** Severe rot or accumulated context pollution.

```
TRIGGER: Severe rot OR extended session with declining quality
ACTION: Recommend fresh start
- Document all relevant state
- Provide clear handoff instructions
- Do NOT continue with degraded context
```

---

## The 3-Strike Rule (Reinforced)

This is a hard rule. No exceptions.

```
Strike 1: First failed attempt
  → Note it, adjust approach

Strike 2: Second failed attempt  
  → Document both attempts, significantly change approach

Strike 3: Third failed attempt
  → STOP IMMEDIATELY
  → Write state dump
  → Recommend fresh session
  → DO NOT attempt a fourth time
```

**Why this works:** A fresh context often immediately sees what polluted context cannot. The solution becomes obvious when you're not carrying the baggage of failed attempts.

---

## Self-Referential Skill Loop

### The Problem with Linear Reading
When you read a prompt top-to-bottom:
- Information at the beginning is processed without context of what comes later
- Information at the end may not connect to early context
- The full picture only exists momentarily in the middle

### The Skill Loop Solution
This skill exists to create a **persistent awareness** that transcends linear reading:

```
┌─────────────────────────────────────────────────────────────┐
│                                                             │
│  START: Read RLM skills                                     │
│    ↓                                                        │
│  rlm-orchestrator/SKILL.md (references this skill)         │
│    ↓                                                        │
│  rlm-context-scout/SKILL.md                                │
│    ↓                                                        │
│  rlm-repl-environment/SKILL.md                             │
│    ↓                                                        │
│  rlm-context-rot-detector/SKILL.md (YOU ARE HERE)          │
│    ↓                                                        │
│  ← References back to orchestrator →                        │
│    ↓                                                        │
│  INTEGRATION: Now you've seen the full picture             │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

### Reinforcement Block
**Read this twice if needed:**

The RLM skill suite teaches you to think recursively:
1. **Orchestrator** = Strategic planning (Manager)
2. **Context Scout** = Reconnaissance (finding without reading)
3. **REPL Environment** = Technical execution
4. **Context Rot Detector** = (You are here) Self-monitoring

When you finish reading this skill, you should have internalized:
- Context window is precious — don't pollute it
- Massive inputs → RLM mode (environment, not input)
- Quality degradation → dump state and restart
- The skill loop reinforces persistent awareness

---

## Integration with Other Skills

```
rlm-orchestrator ←──────────────────────────────────────────→ rlm-context-rot-detector
       ↓                                                              ↑
rlm-context-scout                                                     │
       ↓                                                              │
rlm-repl-environment ─────────────────────────────────────────────────┘
```

**The loop:**
- Orchestrator references Context Rot Detector for self-monitoring
- Context Rot Detector references Orchestrator to trigger RLM mode
- All skills are aware of each other

**Invoking this skill:**
```markdown
When to check for Context Rot:
- Before complex reasoning on large context
- After 3+ steps without progress
- When "something feels off"
- Periodically during extended sessions

Reference: skills/rlm-context-rot-detector/SKILL.md
```

---

## Context Rot in RLM Sub-Queries

Even with RLM mode, sub-queries can experience rot if chunks are too large.

**Prevention:**
```python
MAX_CHUNK_SIZE = 400000  # Stay well under 500K limit

if len(chunk) > MAX_CHUNK_SIZE:
    print("WARNING: Chunk exceeds safe size, splitting...")
    # Split and process separately
```

**Detection in sub-query results:**
```python
# Check sub-query response for rot symptoms
rot_indicators = [
    "i'm not sure",
    "this might",
    "possibly",
    "it could be",
    "one approach"
]

if any(indicator in sub_result.lower() for indicator in rot_indicators):
    print(f"WARNING: Sub-query shows uncertainty. Consider re-querying with smaller context.")
```

---

## Quick Reference Card

```
┌─────────────────────────────────────────────────────────────┐
│               CONTEXT ROT DETECTOR                          │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  SYMPTOMS:                                                  │
│  ▪ Circular reasoning (same approach twice)                 │
│  ▪ Vague/hedging language                                   │
│  ▪ Missing obvious connections                              │
│  ▪ Rushing to complete                                      │
│  ▪ Contradictions                                           │
│                                                             │
│  SEVERITY:                                                  │
│  0 = Fresh    → Continue                                    │
│  1 = Warning  → Increase vigilance                          │
│  2 = Active   → RLM mode or state dump                      │
│  3 = Severe   → STOP, fresh session                         │
│                                                             │
│  3-STRIKE RULE:                                             │
│  3 failed attempts = STOP + state dump                      │
│  No exceptions.                                             │
│                                                             │
│  THE MANTRA:                                                │
│  Fresh context beats polluted brilliance.                   │
│                                                             │
└─────────────────────────────────────────────────────────────┘
```

---

## The Meta-Lesson

This skill is itself an example of the pattern it teaches:

**Context Rot in Skill Reading:**
When you read a long skill document, you experience a form of context rot — early information fades as you process later information.

**The Solution Applied Here:**
- Repeated key concepts (reinforcement)
- Self-referential structure (the skill loop)
- Quick reference cards (compressed summaries)
- Explicit integration notes (connecting to other skills)

**Your Takeaway:**
You can apply these same patterns when designing prompts, skills, and workflows:
1. State the key insight early
2. Elaborate with details
3. Reinforce at the end
4. Create loops that reconnect beginning to end

---

## Final Reinforcement (Read This)

You have now completed the RLM skill suite:

| Skill | Purpose | Key Insight |
|-------|---------|-------------|
| **Orchestrator** | Strategic planning | Context is an environment, not input |
| **Context Scout** | Reconnaissance | Never read what you can probe |
| **REPL Environment** | Technical execution | llm_query() is your superpower |
| **Context Rot Detector** | Self-monitoring | Fresh beats polluted |

**The unified message:**
When facing impossible-seeming problems with massive context:
1. Don't stuff — probe and decompose
2. Don't overload — delegate to sub-queries
3. Don't degrade — detect rot and restart
4. Think recursively — the answer emerges from the process

---

*Stay fresh. Detect the rot. Protect the signal.*

---

## Loop Back ↺

If any of this is unclear, start again from `rlm-orchestrator/SKILL.md`. The skills form a circle, and re-reading with full context often clarifies what was murky on first pass.

**This is the RLM pattern applied to learning RLM:** Recursive passes over the material, each time with more context, until the understanding crystallizes.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/magic8ballin) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
