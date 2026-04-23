---
name: ux-flow-analyzer
description: Analyze user flow complexity, navigation depth, and information architecture in UI screenshots or flow descriptions. Use when reviewing multi-step workflows, evaluating navigation hierarchy, assessing task completion paths, or when user mentions "flow analysis", "navigation depth", "click paths", "user journey", or "information architecture". Use when this capability is needed.
metadata:
  author: unthinkmedia
---

# UX Flow Analyzer

Evaluate navigation complexity, task completion efficiency, and information architecture quality.

## Analysis Workflow

1. **Map the flow** - Identify all screens/states and transitions
2. **Measure depth** - Count navigation levels and decision points
3. **Assess cognitive load** - Evaluate working memory burden
4. **Identify bottlenecks** - Find friction points and dead ends
5. **Generate recommendations** - Suggest improvements with rationale

## Metrics to Evaluate

### Navigation Depth
- **Optimal**: 2-3 clicks to complete primary tasks
- **Acceptable**: 4-5 clicks for secondary tasks
- **Problematic**: 6+ clicks indicates need for shortcuts or restructuring

Count: Starting screen → each click/tap → task completion

### Information Architecture Signals

| Signal | Good | Problematic |
|--------|------|-------------|
| Sidebar items | 5-7 grouped | 15+ ungrouped |
| Menu depth | 2 levels max | 4+ nested levels |
| Breadcrumb length | 3-4 items | 6+ items |
| Primary actions visible | Immediately | Behind menus |

### Cognitive Load Indicators

Evaluate each screen for:
- **Decision points**: How many choices before proceeding?
- **Competing CTAs**: Are primary/secondary actions clear?
- **Context retention**: Must user remember info across screens?
- **Visual hierarchy**: Is most important action obvious?

## Output Format

    # Flow Analysis: [Flow Name]

    ## Summary
    - Total screens: X
    - Max navigation depth: X clicks
    - Decision points: X
    - Cognitive load rating: Low/Medium/High

    ## Flow Map
    [Screen 1] → [Screen 2] → [Screen 3]
                    ↓
               [Screen 2a]

    ## Metrics

    | Metric | Value | Assessment |
    |--------|-------|------------|
    | Clicks to primary task | X | ✅/⚠️/❌ |
    | Sidebar item count | X | ✅/⚠️/❌ |
    | Breadcrumb depth | X | ✅/⚠️/❌ |
    | Visible CTAs per screen | X | ✅/⚠️/❌ |

    ## Issues Found
    1. **[Issue]**: [Description] → [Impact on user]
    2. ...

    ## Recommendations
    1. **[Change]**: [Rationale] → [Expected improvement]
    2. ...

## Common Patterns to Flag

- **Deep nesting**: Primary actions buried 4+ levels deep
- **Sidebar bloat**: 10+ items without clear grouping
- **Dead ends**: Screens with no clear next action
- **Context loss**: User must remember selections across many screens
- **Hidden actions**: Key features behind overflow menus (...) or hover states
- **Modal stacking**: Dialogs opening from dialogs

## References

For detailed guidelines: See [references/depth-guidelines.md](references/depth-guidelines.md)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/unthinkmedia) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
