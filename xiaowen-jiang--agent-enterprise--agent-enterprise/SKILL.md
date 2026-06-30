---
name: performance-optimization
description: Analyze code for performance bottlenecks and recommend optimizations Use when this capability is needed.
metadata:
  author: Xiaowen-Jiang
---

## Process

1. **Identify hot paths** — Which code runs most frequently or handles the most data?
2. **Check algorithmic complexity** — Look for O(n^2) or worse in loops, searches, sorts
3. **Database query analysis**:
   - N+1 queries (loop making individual queries instead of batch)
   - Missing indexes on WHERE/JOIN/ORDER BY columns
   - SELECT * when only specific columns needed
   - Unnecessary subqueries that could be JOINs
4. **Caching opportunities**:
   - Repeated expensive computations
   - Frequently read, rarely updated data
   - API responses that can be cached
5. **Memory usage** — Large objects in memory, memory leaks, unnecessary copies
6. **I/O optimization** — Batch operations, connection pooling, async I/O
7. **Lazy loading** — Defer expensive operations until actually needed

## Output Format

```
## Performance Analysis: [Component/Module]

### Bottlenecks Found (Impact Order)

#### 1. [Bottleneck Title]
- **Location**: file:line
- **Type**: Algorithm / Database / Memory / I/O
- **Current**: O(n^2) nested loop over all users
- **Proposed**: Use a hash map for O(n) lookup
- **Expected Impact**: High / Medium / Low
- **Effort**: Small / Medium / Large

### Quick Wins
- [Changes that are easy to implement with high impact]

### Long-Term Improvements
- [Larger refactors for sustained performance gains]

### Metrics to Track
- [What to measure to verify improvements]
```

## Guidelines

- Profile before optimizing — don't guess where bottlenecks are
- Fix the biggest bottleneck first (Amdahl's law)
- Prefer algorithmic improvements over micro-optimizations
- Consider caching only when data is read-heavy and stale data is acceptable
- Database indexes have write overhead — only add for proven query patterns
- Measure before and after every change
- Don't sacrifice readability for marginal performance gains

---
> Source: [Xiaowen-Jiang/agent-enterprise](https://github.com/Xiaowen-Jiang/agent-enterprise) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-30 -->
