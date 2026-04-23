---
name: context-degradation
description: This skill should be used when the user asks to "diagnose context problems", "fix lost-in-middle issues", "debug agent failures", "understand context poisoning", or mentions context degradation, attention patterns, context clash, or agent performance degradation. Use when this capability is needed.
metadata:
  author: bthillerup
---

# Context Degradation Patterns

Language models exhibit predictable degradation patterns as context length increases. Understanding these patterns is essential for diagnosing failures and designing resilient systems.

## When to Activate

Activate this skill when:
- Agent performance degrades unexpectedly during long conversations
- Debugging cases where agents produce incorrect outputs
- Designing systems that must handle large contexts reliably
- Investigating "lost in middle" phenomena

## Core Degradation Patterns

### The Lost-in-Middle Phenomenon
Models demonstrate U-shaped attention curves. Information at the beginning and end receives reliable attention, while information in the middle suffers 10-40% lower recall accuracy.

**Mitigation**: Place critical information at beginning or end. Use summary structures to surface key information at attention-favored positions.

### Context Poisoning
Hallucinations, errors, or incorrect information enters context and compounds through repeated reference. Once poisoned, context creates feedback loops that reinforce incorrect beliefs.

**Pathways**:
- Tool outputs contain errors accepted as ground truth
- Retrieved documents contain incorrect/outdated information
- Model-generated summaries introduce hallucinations

**Recovery**: Remove/replace poisoned content. May require truncating context or restarting with clean context.

### Context Distraction
As context grows, models over-focus on provided information at the expense of training knowledge. Even a single irrelevant document reduces performance.

**Mitigation**: Apply relevance filtering before loading retrieved documents. Consider whether information truly needs to be in context.

### Context Confusion
Irrelevant information influences responses, especially when context contains multiple task types or when switching between tasks.

**Signs**: Responses address wrong aspect, tool calls seem appropriate for different task, outputs mix requirements from multiple sources.

### Context Clash
Accumulated information directly conflicts, creating contradictory guidance. Differs from poisoning—in clash, multiple correct pieces contradict each other.

**Sources**: Multi-source retrieval conflicts, version conflicts, perspective conflicts.

## Model-Specific Degradation Thresholds

| Model | Degradation Onset | Severe Degradation |
|-------|-------------------|-------------------|
| GPT-5.2 | ~64K tokens | ~200K tokens |
| Claude Opus 4.5 | ~100K tokens | ~180K tokens |
| Claude Sonnet 4.5 | ~80K tokens | ~150K tokens |
| Gemini 3 Pro | ~500K tokens | ~800K tokens |

## The Four-Bucket Mitigation Approach

1. **Write**: Save context outside the window (scratchpads, files)
2. **Select**: Pull relevant context through retrieval and filtering
3. **Compress**: Reduce tokens while preserving information
4. **Isolate**: Split context across sub-agents or sessions

## Guidelines

1. Monitor context length and performance correlation
2. Place critical information at beginning or end
3. Implement compaction triggers before degradation becomes severe
4. Validate retrieved documents before adding to context
5. Use versioning to prevent outdated information causing clash
6. Design for graceful degradation

---

**Created**: 2025-12-20 | **Version**: 1.0.0

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/bthillerup) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
