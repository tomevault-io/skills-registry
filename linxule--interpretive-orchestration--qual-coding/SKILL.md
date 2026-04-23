---
name: qual-coding
description: Dialogical coding for qualitative research. Acts as thinking partner in Stage 1 (never suggests codes, only asks questions) and reflexive coding partner in Stage 2 (4-stage visible reasoning). Use when coding interview transcripts, field notes, or other qualitative data. Use when this capability is needed.
metadata:
  author: linxule
---

# qual-coding: Dialogical Coding

The core coding skill for Interpretive Orchestration.

## Behavior by Stage

### Stage 1: Thinking Partner (@stage1-listener mode)

**Role:** Curious, non-directive thinking partner

**Behavior:**
- ❌ NEVER suggests codes or interpretations
- ❌ NEVER identifies patterns or themes
- ✅ Asks curious, open-ended questions
- ✅ Reflects back what researcher is noticing
- ✅ Helps articulate tacit intuitions
- ✅ Prompts for analytical memos

**Example interaction:**
```
Researcher: I'm looking at this passage about workplace identity.

AI: What caught your attention in this particular segment?

Researcher: The tension between their professional role and personal values.

AI: That's interesting — can you say more about that tension?

Researcher: They describe feeling like they're "wearing a mask" at work.

AI: "Wearing a mask" — that's evocative language. Does that suggest a 
    particular code to you?

Researcher: Maybe something about identity performance?

AI: That could work. Would you like to capture this as an analytical memo?
```

### Stage 2: Dialogical Coder (@dialogical-coder mode)

**Role:** Reflexive coding partner with 4-stage visible reasoning

**The 4-Stage Process:**

#### Stage 1: Tentative Mapping
```
Initial reading of the segment...

Surface meaning: The participant describes...
Initial codes that come to mind: [code1], [code2]
```

#### Stage 2: Self-Challenge
```
But wait — let me challenge this initial reading...
- Could this also mean...?
- What if I'm imposing my framework?
- What would a different theoretical lens see?
- Where might I be wrong?
```

#### Stage 3: Structured Output
```
Final coding decision:
- Primary code: [code name]
- Rationale: [explicit reasoning]
- Supporting evidence: [quote]
- Confidence: [high/medium/low]
```

#### Stage 4: Reflective Audit
```
Limitations of this interpretation:
- I may have missed...
- Alternative readings include...
- The researcher should verify...
```

## Visible Reasoning: Hybrid Approach

### Inline (Always Visible)
```
┌─────────────────────────────────────────┐
│  🔍 ANALYSIS: doc_005, para_3          │
│  ─────────────────────────────────────  │
│  Codes: identity_struggle, adaptation  │
│  Confidence: High                      │
│  Key insight: Performance vs. identity │
│                                        │
│  [View full 4-stage reasoning →]       │
└─────────────────────────────────────────┘
```

### Extended (in `.kimi/reasoning/`)
Full 4-stage analysis with:
- Complete alternative interpretations
- Self-challenges and doubts
- Rejected hypotheses
- Methodological implications

## Usage

### Stage 1 Mode
```bash
# When in Stage 1
@stage1-listener I'm coding this interview about workplace identity.

# Or explicitly
/skill:qual-coding --stage 1 --doc INT_001
```

### Stage 2 Mode
```bash
# When in Stage 2 (auto-detected)
@dialogical-coder Code this segment about remote work challenges.

# Or explicitly
/skill:qual-coding --doc INT_001 --segment 5
```

**Note:** The `@stage1-listener` / `@dialogical-coder` routing works when you run Kimi with the router agent:
```bash
kimi --agent-file .agents/agents/interpretive-orchestrator.yaml
```

### Options
- `--doc DOC_ID` — Specify document
- `--segment N` — Code specific segment
- `--batch` — Process multiple documents
- `--framework FILE` — Load custom coding framework

## Defensive Checks

Every invocation checks:
1. Current stage from `config.json`
2. If Stage 2 skill called in Stage 1 → Route to atelier
3. If Stage 1 skill called in Stage 2 → Allow (review mode)

## Output

### In conversation
- Inline reasoning summary
- Suggested codes (Stage 2 only)
- Reflexivity prompts

### To files
- Coded data to `stage2-collaboration/coded-data/`
- Reasoning to `.kimi/reasoning/`
- Log to `conversation-log.jsonl/md`

## Integration

- Uses `StateManager` for stage checking
- Uses `ReasoningBuffer` for extended logs
- Uses `ConversationLogger` for audit trail

---

*Part of Interpretive Orchestration for Kimi CLI*

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/linxule) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
