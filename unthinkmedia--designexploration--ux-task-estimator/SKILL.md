---
name: ux-task-estimator
description: Estimate time-on-task, click counts, and efficiency metrics for user workflows. Use when measuring task completion efficiency, comparing design alternatives, setting UX benchmarks, or when user mentions "time on task", "click count", "efficiency", "task completion", "benchmark", or "how long does it take". Use when this capability is needed.
metadata:
  author: unthinkmedia
---

# UX Task Estimator

Estimate and measure task completion metrics for UX optimization.

## Core Metrics

### Time on Task (ToT)
Estimated time from task start to completion.

**Components:**
- Reading/scanning time
- Decision time
- Input time
- System response wait time
- Navigation time

### Click/Tap Count
Total interactions required to complete task.

**Types:**
- Navigation clicks
- Selection clicks
- Action clicks (submit, confirm)
- Corrective clicks (back, undo)

### Error Rate Estimate
Likelihood of user making mistakes.

**Factors:**
- Input field complexity
- Ambiguous options
- Lack of validation
- Confusing labels

## Estimation Framework

### Reading Time
Average reading speed: 200-250 words per minute

| Content Type | Time per Item |
|--------------|---------------|
| Short label | 0.5 sec |
| Button text | 0.5 sec |
| Sentence | 2-3 sec |
| Paragraph | 10-15 sec |
| Full form instructions | 15-30 sec |

### Decision Time (Hick's Law)
Base time + (150ms × log2(options + 1))

| Options | Added Time |
|---------|------------|
| 2 | ~300ms |
| 4 | ~450ms |
| 8 | ~600ms |
| 16 | ~750ms |

### Input Time
| Input Type | Time |
|------------|------|
| Click/tap | 0.2 sec |
| Simple text (5 chars) | 2 sec |
| Email address | 4 sec |
| Password | 3 sec |
| Dropdown selection | 1.5 sec |
| Checkbox | 0.5 sec |
| Date picker | 3 sec |

### Navigation Time
| Action | Time |
|--------|------|
| Page load wait | 1-3 sec |
| Visual scan for target | 1-2 sec |
| Mouse/finger movement | 0.3-0.5 sec |
| Scroll action | 0.5 sec |

## Task Analysis Process

1. **List all steps** in the task flow
2. **Categorize each step** (read, decide, input, navigate)
3. **Apply time estimates** per step
4. **Sum total time** and **count interactions**
5. **Add buffer** for errors/confusion (10-20%)

## Output Format

    # Task Analysis: [Task Name]

    **Goal:** [User's objective]
    **Starting Point:** [Where user begins]
    **End Point:** [Task completion criteria]

    ## Step-by-Step Breakdown

    | Step | Action | Type | Time | Clicks |
    |------|--------|------|------|--------|
    | 1 | Read page title | Read | 0.5s | 0 |
    | 2 | Scan navigation | Read | 1.5s | 0 |
    | 3 | Click "APIs" menu | Navigate | 0.5s | 1 |
    | 4 | Wait for page load | Wait | 2s | 0 |
    | 5 | Find target API | Read/Decide | 2s | 0 |
    | 6 | Click API name | Navigate | 0.5s | 1 |
    | ... | ... | ... | ... | ... |

    ## Summary

    | Metric | Value | Benchmark | Status |
    |--------|-------|-----------|--------|
    | Total time | Xs | <Ys | ✅/⚠️/❌ |
    | Click count | X | <Y | ✅/⚠️/❌ |
    | Decision points | X | <Y | ✅/⚠️/❌ |
    | Error risk points | X | 0 | ✅/⚠️/❌ |

    ## Optimization Opportunities

    | Step | Current | Optimized | Savings |
    |------|---------|-----------|---------|
    | [Step] | Xs/Xclicks | Ys/Yclicks | Z% |

    ## Recommendations
    1. **[Change]**: Reduces [metric] by [amount]

## Benchmarks by Task Type

### Simple Action (view, read)
- Target: <10 seconds, <3 clicks
- Example: View dashboard stats

### Standard Action (create, edit)
- Target: <30 seconds, <10 clicks
- Example: Create new item

### Complex Action (configure, setup)
- Target: <2 minutes, <20 clicks
- Example: Configure integration

### Wizard/Flow (multi-step)
- Target: <5 minutes, <30 clicks
- Example: Complete onboarding

## Efficiency Ratios

### Clicks per Goal
Total clicks ÷ number of tasks completed

**Good:** <5 clicks per task
**Acceptable:** 5-10 clicks
**Poor:** >10 clicks

### Time per Click
Total time ÷ total clicks

**Good:** <2 seconds (user flows smoothly)
**Acceptable:** 2-5 seconds (some thinking)
**Poor:** >5 seconds (confusion/waiting)

## Comparison Template

    # Task Comparison: [Task Name]

    | Metric | Current Design | Proposed Design | Improvement |
    |--------|---------------|-----------------|-------------|
    | Time on task | Xs | Ys | Z% faster |
    | Click count | X | Y | Z fewer |
    | Error risk | X points | Y points | Z% safer |
    | Pages visited | X | Y | Z fewer |

## References

For detailed estimation models: See [references/estimation-models.md](references/estimation-models.md)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/unthinkmedia) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
