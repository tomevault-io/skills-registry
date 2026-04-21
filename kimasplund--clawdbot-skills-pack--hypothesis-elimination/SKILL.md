---
name: hypothesis-elimination
description: Diagnostic reasoning for finding root causes. Generates multiple hypotheses, designs tests to eliminate each, and converges on the true cause through evidence. Use when this capability is needed.
metadata:
  author: kimasplund
---

# Hypothesis-Elimination (HE)

Find the root CAUSE of issues through systematic hypothesis testing and elimination.

## When to Use

- Debugging complex issues
- Root cause analysis
- Diagnostic problems
- Evidence can be gathered to test hypotheses
- Multiple possible causes exist

## Process

### Phase 1: Hypothesis Generation
Generate 5-8 possible causes:
```
Symptom: [observed problem]

H1: [possible cause 1]
H2: [possible cause 2]
H3: [possible cause 3]
H4: [possible cause 4]
H5: [possible cause 5]
```

### Phase 2: Test Design
For each hypothesis, design a test:

| Hypothesis | Test | Expected if TRUE | Expected if FALSE |
|------------|------|------------------|-------------------|
| H1 | [test] | [result] | [result] |
| H2 | [test] | [result] | [result] |
| H3 | [test] | [result] | [result] |

### Phase 3: Evidence Gathering
Execute tests in order of:
1. Easiest to perform
2. Most discriminating (eliminates most hypotheses)
3. Lowest risk

### Phase 4: Elimination
After each test, update hypothesis status:
- **ELIMINATED**: Evidence contradicts
- **SUPPORTED**: Evidence consistent
- **INCONCLUSIVE**: Need more data

### Phase 5: Convergence
Continue until:
- One hypothesis remains (root cause found)
- Multiple remain (need more tests)
- All eliminated (missing hypothesis - regenerate)

## Output Template

```markdown
## HE Analysis: [Problem]

### Symptom
[Detailed description of observed issue]

### Hypotheses
| ID | Hypothesis | Prior Confidence |
|----|------------|------------------|
| H1 | [cause] | X% |
| H2 | [cause] | X% |
| H3 | [cause] | X% |

### Tests Performed

#### Test 1: [description]
- **Result**: [observation]
- **Eliminates**: H2, H4
- **Supports**: H1, H3

#### Test 2: [description]
- **Result**: [observation]
- **Eliminates**: H3
- **Supports**: H1

### Elimination Matrix
| Hypothesis | Test 1 | Test 2 | Test 3 | Status |
|------------|--------|--------|--------|--------|
| H1 | ✓ | ✓ | ✓ | **ROOT CAUSE** |
| H2 | ✗ | - | - | Eliminated |
| H3 | ✓ | ✗ | - | Eliminated |
| H4 | ✗ | - | - | Eliminated |

### Root Cause
**H1: [description]**

### Evidence Summary
- [evidence 1 supporting H1]
- [evidence 2 supporting H1]

### Recommended Fix
[action to address root cause]
```

## Example

**Symptom**: API requests failing intermittently

**Hypotheses**:
- H1: Database connection pool exhaustion
- H2: Memory leak causing OOM
- H3: Network timeouts to external service
- H4: Rate limiting by upstream API
- H5: DNS resolution failures

**Tests**:
1. Check connection pool metrics → H1 supported (pool at 100%)
2. Check memory graphs → H2 eliminated (memory stable)
3. Check external service logs → H3 eliminated (all succeed)
4. Check rate limit headers → H4 eliminated (under limit)

**Root Cause**: H1 - Database connection pool exhaustion
**Fix**: Increase pool size, add connection timeout

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kimasplund) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
