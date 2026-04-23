---
name: ux-heuristic-evaluator
description: Systematically evaluate UI designs against Nielsen's 10 Usability Heuristics and other established UX principles. Use when reviewing interfaces for usability issues, conducting heuristic evaluations, assessing design quality, or when user mentions "usability review", "heuristic evaluation", "Nielsen", "usability principles", or "UX audit". Use when this capability is needed.
metadata:
  author: unthinkmedia
---

# UX Heuristic Evaluator

Apply established usability heuristics to identify design issues systematically.

## Evaluation Process

1. Review each screen/state against all 10 heuristics
2. Rate severity of violations found
3. Document specific examples with evidence
4. Prioritize issues by impact and frequency
5. Suggest concrete fixes

## Nielsen's 10 Usability Heuristics

### H1: Visibility of System Status
Keep users informed about what's happening through timely feedback.

**Check for:**
- Loading indicators during async operations
- Progress bars for multi-step processes
- Success/error confirmations after actions
- Current state clearly indicated (selected tab, active filter)

**Violation example:** Form submits with no feedback → user clicks again → duplicate submission

### H2: Match Between System and Real World
Use familiar language and concepts, not system-oriented terms.

**Check for:**
- Jargon-free labels
- Icons that match real-world metaphors
- Logical information organization
- Natural reading order

**Violation example:** "Instantiate new resource entity" instead of "Create new item"

### H3: User Control and Freedom
Provide clear exits and undo capabilities.

**Check for:**
- Cancel buttons on all dialogs
- Undo for destructive actions
- Easy navigation back
- Clear way to exit current flow

**Violation example:** Delete action with no confirmation or undo option

### H4: Consistency and Standards
Follow platform conventions and maintain internal consistency.

**Check for:**
- Consistent button placement
- Same terms for same concepts
- Standard icons for common actions
- Predictable interaction patterns

**Violation example:** "Save" button on left in some dialogs, right in others

### H5: Error Prevention
Prevent problems before they occur.

**Check for:**
- Confirmation for destructive actions
- Input validation before submission
- Disabled states for invalid actions
- Clear constraints communicated upfront

**Violation example:** User can submit form with invalid email, only learns error after server response

### H6: Recognition Rather Than Recall
Minimize memory load by making options visible.

**Check for:**
- Visible options rather than requiring memorization
- Contextual help when needed
- Clear labels on all controls
- Recent/suggested items shown

**Violation example:** User must remember exact command syntax instead of seeing available commands

### H7: Flexibility and Efficiency of Use
Provide accelerators for expert users without confusing novices.

**Check for:**
- Keyboard shortcuts available
- Customizable workflows
- Bulk actions for power users
- Sensible defaults for quick completion

**Violation example:** No keyboard shortcuts; every action requires multiple mouse clicks

### H8: Aesthetic and Minimalist Design
Remove irrelevant or rarely needed information.

**Check for:**
- Visual hierarchy guides attention
- No competing elements
- Whitespace used effectively
- Only necessary information shown

**Violation example:** Dashboard shows 20 metrics with equal visual weight

### H9: Help Users Recognize, Diagnose, and Recover from Errors
Error messages should be clear, specific, and constructive.

**Check for:**
- Plain language error messages
- Specific problem identification
- Constructive suggestions for resolution
- No error codes without explanation

**Violation example:** "Error 0x80070005" with no explanation

### H10: Help and Documentation
Provide easy-to-search, task-focused documentation.

**Check for:**
- Contextual help available
- Searchable documentation
- Task-oriented guidance
- Tooltips for complex features

**Violation example:** Only help is a PDF manual with no search

## Severity Rating Scale

| Rating | Severity | Description |
|--------|----------|-------------|
| 0 | Not a problem | No usability issue |
| 1 | Cosmetic | Fix only if extra time |
| 2 | Minor | Low priority fix |
| 3 | Major | High priority, significant impact |
| 4 | Catastrophic | Must fix before release |

## Output Format

    # Heuristic Evaluation: [Product/Feature Name]

    ## Summary
    - Violations found: X
    - Critical (severity 4): X
    - Major (severity 3): X
    - Minor (severity 1-2): X

    ## Findings by Heuristic

    ### H1: Visibility of System Status
    **Rating:** X/4
    **Issues:**
    - [Issue description] | Severity: X | Location: [screen/component]

    ### H2: Match Between System and Real World
    ... [repeat for each heuristic]

    ## Priority Recommendations
    1. **[Highest impact fix]** - Addresses: H#, H# | Severity: X
    2. **[Next fix]** - Addresses: H# | Severity: X
    ...

## References

For extended heuristic details and examples: See [references/heuristics-detail.md](references/heuristics-detail.md)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/unthinkmedia) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
