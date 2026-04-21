---
name: triumvirate
description: 3x3 Parallel Debate system for high-confidence research. Spawns 9 agent instances (3 Proposer/Skeptic/Judge cohorts) to argue, critique, and adjudicate claims. Use for complex queries, controversial topics, or when hallucination-free research is critical. Use when this capability is needed.
metadata:
  author: stephenwinters81
---

# The Triumvirate: Truth-Seeking Research Protocol

## When to Activate
Use this skill when the user requests:
- Research on complex or contested topics
- High-confidence fact verification
- Analysis where hallucinations are unacceptable
- Scientific or technical claims that need rigorous validation
- User explicitly mentions "triumvirate" or "debate analysis"

## Architecture
**9 Agent Instances** organized as:

### The Cohort (x3 parallel units)
1. **Proposer** - Builds the strongest affirmative case with evidence
2. **Skeptic** - Attacks the argument for logical fallacies, assumptions, overgeneralizations
3. **Judge** - Impartially determines which claims are reality-compliant

### The Synthesis
A final meta-analysis compares all three Judge verdicts to identify:
- High Confidence Findings (all cohorts agree)
- Areas of Uncertainty (cohorts disagree)
- Refuted/Hallucinated Claims (caught by Skeptics)

## Invocation

```bash
python3 ~/.claude/skills/triumvirate/triumvirate.py "Your research query here"
```

## Example Usage

User: "What is the actual efficacy of intermittent fasting for weight loss?"

```bash
python3 ~/.claude/skills/triumvirate/triumvirate.py "What is the actual efficacy of intermittent fasting for weight loss?"
```

## Why This Works
1. **Skeptic Isolation**: The Skeptic is prompted as an "adversary," breaking sycophancy loops
2. **Condorcet Jury Theorem**: 3 independent cohorts act as error-correctors during synthesis
3. **Logical Fallacy Detection**: Skeptics explicitly check for correlation/causation errors, cherry-picking, survivorship bias, etc.

## Requirements
- Claude Code CLI installed and authenticated (`claude` command available)
- Python package: `rich` (for formatted output)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/stephenwinters81) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
