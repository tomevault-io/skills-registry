---
name: weaver
description: Map-Reduce system for comprehensive reports and literature reviews. Breaks topic into chapters, researches each in parallel, then weaves into unified document. Use for deep research, reports, and synthesis. Use when this capability is needed.
metadata:
  author: stephenwinters81
---

# The Weaver: Deep Research Synthesis Protocol

## When to Activate
Use this skill when the user requests:
- Comprehensive literature reviews
- Long-form reports or documents
- Deep research on complex topics
- Multi-chapter synthesis
- User explicitly mentions "weaver", "report", or "literature review"

## Architecture
**Map-Reduce with Editorial Synthesis**

### Phase 1: The Loom (Map)
- **Planner** agent breaks topic into Table of Contents
- Typically 5-10 chapters/sections
- Each chapter has clear scope and questions

### Phase 2: Spinning (Execute)
- Spawn N parallel **Worker** agents (one per chapter)
- Each worker researches their chapter deeply
- Workers operate independently (no cross-talk)

### Phase 3: Weaving (Reduce)
- **Editor** agent ingests all chapter reports
- Identifies gaps, contradictions, redundancies
- Stitches into coherent Master Document
- Adds transitions, executive summary, conclusions

## Invocation

```bash
python3 ~/.claude/skills/weaver/weaver.py "Your research topic"
```

## Options

```bash
python3 ~/.claude/skills/weaver/weaver.py "Your topic" --chapters 8 --output report.md
```

## Example Usage

User: "Write a comprehensive report on Australia's critical minerals opportunity"

```bash
python3 ~/.claude/skills/weaver/weaver.py "Australia's critical minerals opportunity: resources, processing, markets, and policy"
```

## Output Structure
```
1. Executive Summary
2. [Chapter 1 - from Worker 1]
3. [Chapter 2 - from Worker 2]
...
N. Conclusions and Recommendations
N+1. Sources and Further Reading
```

## Why This Works
1. **Parallel Depth**: Each chapter gets dedicated deep research
2. **No Context Limits**: Workers don't share context, avoiding truncation
3. **Editorial Coherence**: Final pass ensures unified voice and flow
4. **Comprehensive Coverage**: Structured approach ensures no gaps

## Requirements
- Claude Code CLI installed and authenticated (`claude` command available)
- Python package: `rich` (for formatted output)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/stephenwinters81) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
