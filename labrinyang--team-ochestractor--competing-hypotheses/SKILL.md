---
name: competing-hypotheses
description: Use when debugging with unclear root cause and multiple plausible explanations that need parallel adversarial testing to converge on the answer
metadata:
  author: labrinyang
---

# Competing Hypotheses: Adversarial Debugging Pattern

## Overview

When the root cause is unclear, a single investigator anchors on the first plausible explanation. Multiple investigators testing competing theories — and actively trying to disprove each other — converge faster and more accurately.

**Core principle:** Adversarial debate eliminates weak hypotheses. The theory that survives structured attack is most likely correct.

**Management theory:** Psychological Safety (agents MUST challenge each other), Tuckman's Storming (debate is the mechanism, not a problem), Belbin Monitor-Evaluator (devil-advocate role is mandatory).

## When to Use

- Bug with unclear root cause
- Multiple plausible explanations exist
- Single investigator would likely anchor on first theory
- Reproducing the bug is possible but cause is ambiguous

**Don't use when:**
- Root cause is obvious (just fix it)
- Only one plausible explanation
- Bug cannot be reproduced
- Debugging requires sequential steps (A reveals B reveals C)

## Team Composition

```
coordinator (lead, acts as judge)
├── debugger × 2-5     (each tests one hypothesis)
└── devil-advocate × 1 (challenges all hypotheses)
```

**Belbin coverage:**
- Thinking: devil-advocate (Monitor-Evaluator)
- Action: debuggers (Shaper — drives toward root cause)
- People: coordinator (Coordinator — judges evidence)

**Sizing by complexity:**

| Bug Complexity | Debuggers | Notes |
|---------------|-----------|-------|
| 2 plausible causes | 2 | Minimum viable debate |
| 3-4 theories | 3-4 | Standard case |
| Systemic/unknown | 4-5 + devil-advocate | Maximum investigation |

## The Process

### Phase 1: Forming — Hypothesis Generation

Coordinator:
1. Describes the bug symptoms clearly
2. Lists all plausible hypotheses (or asks team to generate them)
3. Assigns one hypothesis per debugger
4. Spawns devil-advocate to challenge all of them

**Spawn prompt template for debugger:**
```
You are testing the hypothesis: [HYPOTHESIS]

Bug symptoms: [SYMPTOMS]
Reproduction steps: [STEPS]

Your job:
1. Find evidence FOR your hypothesis (prove it)
2. Find evidence AGAINST your hypothesis (disprove it)
3. Be honest — if your hypothesis is wrong, say so
4. Report: evidence for, evidence against, confidence (0-100%)

You MUST report disconfirming evidence. Hiding evidence that
disproves your hypothesis is a critical failure.
```

**Devil-advocate spawn prompt:**
```
You will review each debugger's findings.

For EACH hypothesis:
1. What evidence would definitively prove it? Did they find it?
2. What evidence would definitively disprove it? Did they look?
3. Are there alternative explanations for their evidence?
4. What tests would distinguish this hypothesis from others?

Your success metric is finding flaws. Approving a weak hypothesis
without challenge is a failure.
```

### Phase 2: Storming — Parallel Investigation

Each debugger independently:
- Investigates their assigned hypothesis
- Collects evidence (code, logs, test results)
- Reports both confirming AND disconfirming evidence
- Assigns confidence score

**Critical:** Debuggers must report disconfirming evidence. The prompt explicitly requires this to prevent confirmation bias.

### Phase 3: Norming — Adversarial Debate

```
For each hypothesis:
  1. Debugger presents: evidence for + against + confidence
  2. Devil-advocate challenges: gaps, alternative explanations
  3. Other debuggers challenge: "my evidence contradicts yours because..."
  4. Coordinator scores: STRONG / WEAK / DISPROVEN

Elimination round:
  - DISPROVEN hypotheses are discarded
  - WEAK hypotheses get one more investigation round
  - STRONG hypotheses proceed to verification
```

**The debate IS the value.** Sequential investigation suffers from anchoring. Parallel adversarial investigation eliminates weak theories faster.

### Phase 4: Performing — Verification

For the surviving hypothesis:
1. Debugger implements the fix
2. Verify: does the fix resolve the symptoms?
3. Regression: does the fix break anything else?
4. Devil-advocate: "Is this really the root cause, or are we masking the symptom?"

### Phase 5: Adjourning — Record & Reflect

- Document: the winning hypothesis, eliminated hypotheses, and why
- Record to `.claude.md`: what theories were wrong and why (prevents future anchoring)
- Trigger team-orchestrator:session-reflection

## Example: App Exits After One Message

```
Coordinator identifies 5 hypotheses:
  H1: WebSocket connection closing prematurely
  H2: Event loop draining with no listeners
  H3: Unhandled promise rejection causing exit
  H4: Session timeout misconfigured
  H5: Message handler throwing uncaught error

Team investigates in parallel:

  Debugger 1 (H1): "WebSocket stays open — DISPROVEN"
  Debugger 2 (H2): "Event loop has active listeners — DISPROVEN"
  Debugger 3 (H3): "Found unhandled rejection in auth middleware — STRONG (80%)"
  Debugger 4 (H4): "Timeout is 30min, app exits in 1s — DISPROVEN"
  Debugger 5 (H5): "Message handler has try-catch — WEAK (30%)"

  Devil-advocate: "H3 is strong but — does the rejection happen on EVERY
    message or just the first? If first-only, the auth token refresh
    might be the real cause, not the handler."

  → Follow-up reveals: auth token refresh throws on first use because
    token is not yet set. H3 was close but the real root cause is
    token initialization order.

Without adversarial debate, team would have patched the rejection
handler without fixing the token initialization.
```

## Common Mistakes

| Mistake | Fix |
|---------|-----|
| Debugger hides disconfirming evidence | Prompt explicitly requires both-sides reporting |
| All hypotheses are variations of one idea | Ensure hypotheses are truly independent |
| Skipping debate — just picking highest confidence | Debate reveals flaws that confidence scores don't |
| Devil-advocate too soft | Prompt: "approving a weak hypothesis is a failure" |
| Not recording eliminated hypotheses | They prevent future anchoring — record them |
| Fixing symptom, not root cause | Devil-advocate's final question prevents this |

## Integration

**Pre-requisite:** team-orchestrator:orchestrating-work routes here
**Post-requisite:** team-orchestrator:session-reflection records learnings
**Related:** superpowers:systematic-debugging for single-agent debugging

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/labrinyang) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
