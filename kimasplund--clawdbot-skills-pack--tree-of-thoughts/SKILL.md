---
name: tree-of-thoughts
description: Systematic evaluation methodology for finding THE best solution among known options. Explores multiple reasoning paths, scores branches, and prunes low-confidence paths. Use when this capability is needed.
metadata:
  author: kimasplund
---

# Tree of Thoughts (ToT)

Find THE best solution by systematically exploring and evaluating multiple reasoning paths.

## When to Use

- Clear success criteria exist
- Multiple known options to evaluate
- Need THE best answer (not all options)
- Time permits thorough analysis

## Process

### Phase 1: Branch Generation
Generate 3-5 distinct approaches to the problem:
```
Root Problem
├── Approach A: [description]
├── Approach B: [description]
├── Approach C: [description]
└── Approach D: [description]
```

### Phase 2: Branch Evaluation
Score each branch on defined criteria (1-10):

| Approach | Feasibility | Impact | Risk | Effort | Total |
|----------|-------------|--------|------|--------|-------|
| A | ? | ? | ? | ? | ? |
| B | ? | ? | ? | ? | ? |
| C | ? | ? | ? | ? | ? |

### Phase 3: Pruning
Eliminate branches scoring below threshold (typically 60%):
- Keep top 2-3 branches
- Document why others were pruned
- Note any branches worth revisiting

### Phase 4: Deep Exploration
For remaining branches, explore sub-options:
```
Approach A (Score: 8.2)
├── Variant A1: [refinement]
├── Variant A2: [refinement]
└── Variant A3: [refinement]
```

### Phase 5: Final Selection
Compare top candidates:
1. Weighted scoring against criteria
2. Risk-adjusted evaluation
3. Implementation complexity
4. Select winner with confidence score

## Output Template

```markdown
## ToT Analysis: [Problem]

### Branches Explored
1. **[Approach A]** - [1-line description]
2. **[Approach B]** - [1-line description]
3. **[Approach C]** - [1-line description]

### Evaluation Matrix
| Approach | [Criteria 1] | [Criteria 2] | [Criteria 3] | Score |
|----------|--------------|--------------|--------------|-------|
| A | X/10 | X/10 | X/10 | X.X |
| B | X/10 | X/10 | X/10 | X.X |
| C | X/10 | X/10 | X/10 | X.X |

### Pruned (with reasons)
- [Approach D]: [why eliminated]

### Recommendation
**Winner: [Approach X]**
- Confidence: [high/medium/low]
- Key advantages: [list]
- Risks to monitor: [list]
```

## Example

**Problem**: Choose database for real-time analytics

**Branches**:
1. PostgreSQL with TimescaleDB
2. ClickHouse
3. Apache Druid
4. Elasticsearch

**Evaluation** (criteria: query speed, scalability, ops complexity):
- ClickHouse: 9 + 9 + 7 = 25 ← Winner
- TimescaleDB: 7 + 7 + 9 = 23
- Druid: 8 + 9 + 5 = 22
- Elasticsearch: 6 + 8 + 6 = 20 (pruned)

**Result**: ClickHouse with 83% confidence

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kimasplund) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
