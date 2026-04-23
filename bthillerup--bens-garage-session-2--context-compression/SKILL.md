---
name: context-compression
description: This skill should be used when the user asks to "compress context", "summarize conversation history", "implement compaction", "reduce token usage", or mentions context compression, structured summarization, or long-running agent sessions exceeding context limits. Use when this capability is needed.
metadata:
  author: bthillerup
---

# Context Compression Strategies

When agent sessions generate millions of tokens, compression becomes mandatory. The naive approach is aggressive compression to minimize tokens per request. The **correct optimization target is tokens per task**: total tokens consumed to complete a task, including re-fetching costs when compression loses critical information.

## When to Activate

Activate this skill when:
- Agent sessions exceed context window limits
- Codebases exceed context windows (5M+ token systems)
- Designing conversation summarization strategies
- Debugging cases where agents "forget" what files they modified

## Core Approaches

### 1. Anchored Iterative Summarization (Recommended)
Maintain structured, persistent summaries with explicit sections. When compression triggers, summarize only newly-truncated span and merge with existing summary.

**Key insight**: Structure forces preservation. Dedicated sections act as checklists.

### 2. Opaque Compression
Compressed representations optimized for reconstruction fidelity. Achieves 99%+ compression but sacrifices interpretability.

### 3. Regenerative Full Summary
Generate detailed summaries on each compression. Readable but may lose details across repeated compression cycles.

## The Artifact Trail Problem

Artifact trail integrity is universally weak (2.2-2.5 out of 5.0). Coding agents need to know:
- Which files were created/modified
- What changed in each file
- Function names, variable names, error messages

**Solution**: Separate artifact index or explicit file-state tracking in agent scaffolding.

## Structured Summary Sections

```markdown
## Session Intent
[What the user is trying to accomplish]

## Files Modified
- auth.controller.ts: Fixed JWT token generation
- config/redis.ts: Updated connection pooling

## Decisions Made
- Using Redis connection pool instead of per-request
- Retry logic with exponential backoff

## Current State
- 14 tests passing, 2 failing

## Next Steps
1. Fix remaining test failures
2. Run full test suite
```

## Compression Triggers

| Strategy | Trigger Point | Trade-off |
|----------|---------------|-----------|
| Fixed threshold | 70-80% utilization | Simple but may compress too early |
| Sliding window | Keep last N turns + summary | Predictable context size |
| Importance-based | Compress low-relevance first | Complex but preserves signal |
| Task-boundary | Compress at logical completions | Clean summaries |

## Compression Performance

| Method | Compression Ratio | Quality Score |
|--------|-------------------|---------------|
| Anchored Iterative | 98.6% | 3.70 |
| Regenerative | 98.7% | 3.44 |
| Opaque | 99.3% | 3.35 |

The 0.7% additional tokens retained by structured summarization buys 0.35 quality points—worth it when re-fetching costs matter.

## Guidelines

1. Optimize for tokens-per-task, not tokens-per-request
2. Use structured summaries with explicit file tracking sections
3. Trigger compression at 70-80% context utilization
4. Implement incremental merging rather than full regeneration
5. Track artifact trail separately if file tracking is critical
6. Monitor re-fetching frequency as a compression quality signal

---

**Created**: 2025-12-22 | **Version**: 1.1.0

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/bthillerup) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
