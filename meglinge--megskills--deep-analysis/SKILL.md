---
name: deep-analysis
description: Uses multiple parallel finder agents and multiple oracle consultations simultaneously for broad analysis. Use when asked to search from multiple angles, investigate across many files, or need parallel multi-perspective analysis. Use when this capability is needed.
metadata:
  author: meglinge
---

# Deep Analysis

Performs thorough codebase investigation using parallel search agents and multiple oracle consultations for comprehensive analysis.

## When to Use

- Complex debugging across multiple files
- Architecture or design pattern analysis
- Understanding cross-cutting concerns
- Investigating system-wide issues
- Deep code review and quality assessment

## Workflow

### Phase 1: Parallel Search

Launch multiple `finder` agents simultaneously to explore different aspects:

```
1. Search for core functionality: finder("Find the main implementation of [feature]")
2. Search for related components: finder("Find all components that interact with [feature]")
3. Search for tests: finder("Find all tests related to [feature]")
4. Search for configuration: finder("Find configuration and settings for [feature]")
```

Execute all finder calls in parallel for maximum efficiency.

### Phase 2: Multi-Oracle Analysis

After gathering search results, consult the oracle multiple times for different perspectives:

```
1. Oracle #1 - Architecture Review:
   oracle(task: "Review the architecture of [feature]", files: [relevant_files])

2. Oracle #2 - Code Quality Analysis:
   oracle(task: "Analyze code quality, patterns, and potential issues", files: [relevant_files])

3. Oracle #3 - Integration Analysis:
   oracle(task: "Analyze how components integrate and identify coupling issues", files: [relevant_files])
```

### Phase 3: Synthesis

Combine insights from all oracles to provide:
- Comprehensive understanding of the system
- Identified issues and their root causes
- Actionable recommendations
- Priority ranking of findings

## Best Practices

1. **Parallel Execution**: Always run independent finder calls in a single message block
2. **Focused Queries**: Each finder/oracle should have a specific, focused objective
3. **Progressive Depth**: Start broad, then drill down into problem areas
4. **Cross-Reference**: Use oracle findings to guide additional targeted searches
5. **Document Findings**: Use todo_write to track discoveries and action items

## Example Usage

For investigating a performance issue:

```
Phase 1 - Parallel Search:
- finder("Find database query implementations")
- finder("Find caching mechanisms")
- finder("Find API endpoint handlers")
- finder("Find performance-related tests or benchmarks")

Phase 2 - Oracle Analysis:
- oracle: "Analyze database query patterns for N+1 issues"
- oracle: "Review caching strategy effectiveness"
- oracle: "Identify bottlenecks in request handling"

Phase 3 - Synthesize and Report
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/meglinge) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
