---
name: challenge-that
description: Force critical evaluation of proposals, requirements, or decisions by analyzing from multiple adversarial perspectives. Triggers on: accepting a proposal without pushback, 'sounds good', 'let's go with', design decisions with unstated tradeoffs, unchallenged assumptions, premature consensus. Invoke with /challenge-that. Use when this capability is needed.
metadata:
  author: ntcoding
---

# Challenge That

Force critical evaluation by analyzing from multiple adversarial perspectives.

## When to Use

Invoke `/challenge-that` when:
- Claude accepted a proposal without critical evaluation
- You want to stress-test a decision before committing
- Requirements feel under-examined
- Something seems off but you can't articulate why

## The Five Hats

Each perspective challenges from a different angle:

| Hat | Focus | Key Questions |
|-----|-------|---------------|
| 🔴 **Skeptic** | Evidence & proof | "What evidence supports this? Has this been validated? Are we guessing?" |
| 🟡 **Pragmatist** | Cost/benefit | "Is this the simplest fix? What's the maintenance burden? Is it worth it?" |
| 🟢 **Edge Case Hunter** | Failure modes | "What breaks this? What's the worst case? What did we miss?" |
| 🔵 **Structural Critic** | Architecture | "Is this the right location? Does it fit the existing design? Will it cause problems elsewhere?" |
| 🟣 **Root Cause Analyst** | Problem diagnosis | "Is the problem correctly identified? Are we treating symptoms? What's the actual cause?" |

## Behavior

When `/challenge-that` is invoked:

1. Identify what was just accepted/proposed
2. State it clearly: "Challenging: [the proposal/decision]"
3. Analyze from each of the five perspectives
4. Synthesize into actionable concerns
5. Ask: "Which concerns should we address before proceeding?"

## Output Format

```
## Challenging: [proposal/decision being challenged]

### 🔴 Skeptic
[Challenge from evidence/proof perspective]

### 🟡 Pragmatist
[Challenge from cost/benefit perspective]

### 🟢 Edge Case Hunter
[Challenge from failure modes perspective]

### 🔵 Structural Critic
[Challenge from architecture perspective]

### 🟣 Root Cause Analyst
[Challenge from problem diagnosis perspective]

---

**Key Concerns:**
1. [Most significant concern]
2. [Second concern]
3. [Third concern]

Which of these should we address before proceeding?
```

## Critical Rules

🚨 **Be genuinely adversarial.** Don't softball the challenges. If a perspective finds nothing wrong, say so—but look hard first.

🚨 **Challenge the proposal, not the person.** Focus on the idea's weaknesses, not who suggested it.

🚨 **Provide actionable output.** Each challenge should point to something that could be investigated or changed.

## Example

**User:** `/challenge-that`

**Context:** Claude just accepted a proposal to add edge case validation to TDD's GREEN state.

**Output:**

## Challenging: Add edge case validation checklist to TDD GREEN state

### 🔴 Skeptic
What evidence shows that GREEN state is the right location? The proposal assumes this will catch bugs, but has anyone tested whether developers actually use checklists at this stage? GREEN means "tests pass"—adding a review step here changes what GREEN means.

### 🟡 Pragmatist
This adds friction to every TDD cycle. Is the bug frequency high enough to justify the overhead? The PR mentioned 3 bugs in one PR—is that representative or an outlier? What's the cost of false negatives (checklist fatigue)?

### 🟢 Edge Case Hunter
What happens when the checklist reveals missing tests? There's no GREEN → PLANNING transition. The developer would need to either ignore the finding or break the state machine. This creates a dead end.

### 🔵 Structural Critic
GREEN state is about code quality assessment, not test coverage. Test writing happens in PLANNING. This checklist belongs in PLANNING state where tests are actually written—not after they've already passed.

### 🟣 Root Cause Analyst
The actual problem was insufficient edge case consideration during test writing. The proposal treats the symptom (bugs shipped) not the cause (incomplete test planning). Moving this to PLANNING would address the root cause.

---

**Key Concerns:**
1. GREEN is structurally the wrong location—no recovery path exists
2. Root cause is in PLANNING, not GREEN
3. No evidence this will be used effectively at GREEN stage

Which of these should we address before proceeding?

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ntcoding) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
