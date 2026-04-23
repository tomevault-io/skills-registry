---
name: ux-cognitive-load-checker
description: Measure and reduce cognitive load in UI designs by analyzing decision points, working memory demands, and information density. Use when evaluating screen complexity, identifying overwhelming interfaces, optimizing for user attention, or when user mentions "cognitive load", "overwhelm", "too many options", "decision fatigue", "complexity", or "simplify". Use when this capability is needed.
metadata:
  author: unthinkmedia
---

# UX Cognitive Load Checker

Evaluate working memory burden and identify opportunities to reduce mental effort.

## Cognitive Load Types

### 1. Intrinsic Load
Inherent complexity of the task itself. Cannot be eliminated, must be managed.

**Examples:** Form with many required fields, complex configuration options

### 2. Extraneous Load
Unnecessary cognitive burden from poor design. Should be eliminated.

**Examples:** Unclear labels, inconsistent patterns, irrelevant information

### 3. Germane Load
Productive mental effort that aids learning. Should be optimized.

**Examples:** Helpful tutorials, meaningful categorization, progressive disclosure

## Evaluation Metrics

### Decision Points per Screen
Count distinct choices user must make:

| Count | Rating | Action |
|-------|--------|--------|
| 1-3 | Optimal | Maintain |
| 4-6 | Acceptable | Monitor |
| 7-9 | High | Reduce |
| 10+ | Overwhelming | Redesign |

### Information Density
Evaluate content-to-space ratio:

- [ ] Is whitespace used to group related items?
- [ ] Are there visual breaks between sections?
- [ ] Can user scan quickly to find key info?
- [ ] Is there a clear visual hierarchy?

### Working Memory Demands
Does user need to remember information?

- [ ] Selections persisted visibly (not just in memory)
- [ ] Cross-reference info shown in context
- [ ] No need to navigate away to recall data
- [ ] Chunking used for long lists (5-7 items per group)

### Attention Competition
Do elements compete for focus?

- [ ] Single primary CTA per screen
- [ ] Clear visual hierarchy guides eye
- [ ] No more than 2-3 items at same visual weight
- [ ] Animations/movements minimized

## Hick's Law Application

Time to decide increases logarithmically with number of choices.

**Formula:** RT = a + b × log2(n)

**Practical guidance:**

| Choices | Decision Time | Recommendation |
|---------|--------------|----------------|
| 2 | ~300ms | Ideal for quick decisions |
| 4 | ~450ms | Good for common options |
| 8 | ~600ms | Consider grouping |
| 16 | ~750ms | Must categorize |
| 32+ | ~900ms+ | Requires search/filter |

## Miller's Law Application

Working memory holds 7 ± 2 items.

**Practical limits:**
- Navigation items: 5-9 maximum
- Form sections: 3-5 visible at once
- List items before pagination: 7-10
- Tabs: 5-7 maximum

## Red Flags Checklist

### Visual Overload
- [ ] More than 3 font sizes visible
- [ ] More than 5 colors (excluding images)
- [ ] Dense text blocks without headers
- [ ] Multiple competing animations

### Decision Overload
- [ ] All options presented at equal weight
- [ ] No recommended/default option
- [ ] Too many choices without filtering
- [ ] No clear "next step" path

### Memory Demands
- [ ] User must remember previous selections
- [ ] Information referenced but not shown
- [ ] Long forms without progress saving
- [ ] Complex multi-step without summary

## Output Format

    # Cognitive Load Assessment: [Screen/Feature Name]

    ## Summary
    - Overall load rating: Low/Medium/High/Critical
    - Decision points: X
    - Information density: X%
    - Working memory items: X

    ## Load Breakdown

    | Load Type | Level | Sources |
    |-----------|-------|---------|
    | Intrinsic | [Level] | [Task complexity factors] |
    | Extraneous | [Level] | [Design issues] |
    | Germane | [Level] | [Learning aids present] |

    ## Metrics

    | Metric | Value | Threshold | Status |
    |--------|-------|-----------|--------|
    | Decision points | X | ≤6 | ✅/⚠️/❌ |
    | CTAs competing | X | ≤2 | ✅/⚠️/❌ |
    | Memory items | X | ≤7 | ✅/⚠️/❌ |
    | Visual weight levels | X | ≤3 | ✅/⚠️/❌ |

    ## Issues Found
    1. **[Issue]**: [Description] → [Cognitive impact]

    ## Recommendations
    1. **[Change]**: [Rationale] → [Expected load reduction]

## Reduction Strategies

### Simplify
- Remove non-essential options
- Hide advanced features behind "Show more"
- Use smart defaults

### Chunk
- Group related items (max 7 per group)
- Use headers/dividers
- Create logical sections

### Sequence
- Break into steps
- Progressive disclosure
- Wizard patterns for complex tasks

### Support
- Show context inline
- Persist selections visibly
- Provide recommendations

## References

For cognitive psychology principles: See [references/cognitive-principles.md](references/cognitive-principles.md)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/unthinkmedia) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
