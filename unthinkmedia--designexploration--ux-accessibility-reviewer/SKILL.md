---
name: ux-accessibility-reviewer
description: Evaluate UX accessibility beyond WCAG compliance, including keyboard navigation flows, screen reader experience, cognitive accessibility, and inclusive design patterns. Use when reviewing interfaces for accessibility UX, assessing keyboard workflows, checking focus management, or when user mentions "accessibility UX", "a11y review", "keyboard navigation", "screen reader", "inclusive design", or "cognitive accessibility". Use when this capability is needed.
metadata:
  author: unthinkmedia
---

# UX Accessibility Reviewer

Evaluate accessibility from a user experience perspective, beyond technical compliance.

## Review Scope

This skill focuses on **accessibility UX** - how accessible the experience is, not just whether code passes automated checks.

Key difference:
- **WCAG compliance**: "Alt text exists" ✓
- **Accessibility UX**: "Alt text is actually helpful" ✓

## Evaluation Areas

### 1. Keyboard Navigation UX

**Evaluate the complete keyboard-only experience:**

- [ ] Can all interactive elements be reached via Tab?
- [ ] Is tab order logical (follows visual flow)?
- [ ] Is focus indicator always visible?
- [ ] Can complex widgets be navigated (arrow keys in menus)?
- [ ] Are keyboard shortcuts discoverable and documented?
- [ ] Can user escape from any component (Escape key)?
- [ ] Is focus trapped appropriately in modals?
- [ ] Is focus returned to trigger when modal closes?

**Common issues:**
| Issue | Impact | Fix |
|-------|--------|-----|
| Focus lost after action | User disoriented | Return focus to next logical element |
| Skip links missing | Long navigation to content | Add "Skip to main content" link |
| Custom controls not keyboard accessible | Cannot use without mouse | Add proper ARIA and key handlers |
| Focus trap missing in modal | Focus escapes behind overlay | Implement focus trap |

### 2. Screen Reader Experience

**Think in announcements, not visuals:**

- [ ] Does heading structure make sense read aloud?
- [ ] Are buttons and links labeled with their action?
- [ ] Are form fields properly associated with labels?
- [ ] Are error messages announced when they appear?
- [ ] Are live regions used for dynamic updates?
- [ ] Is decorative content properly hidden?
- [ ] Are complex widgets described with ARIA?
- [ ] Is reading order logical?

**Good vs bad labels:**
| Element | Bad | Good |
|---------|-----|------|
| Icon button | (no label) | "Delete item" |
| Link | "Click here" | "View documentation" |
| Image | "image.png" | "Dashboard showing monthly revenue" |
| Form field | (placeholder only) | Label: "Email address" |

### 3. Focus Management

**Track focus through interactions:**

- [ ] Focus moves to new content when loaded dynamically
- [ ] Focus moves to modal when opened
- [ ] Focus returns to trigger after modal closes
- [ ] Focus moves to error summary after form submission fails
- [ ] Focus is not unexpectedly moved
- [ ] Focus order in forms matches visual order

### 4. Cognitive Accessibility

**Reduce mental burden:**

- [ ] Instructions are clear and concise
- [ ] Errors explain what went wrong and how to fix
- [ ] Time limits are adjustable or have warnings
- [ ] Important actions require confirmation
- [ ] Progress is clearly indicated in multi-step flows
- [ ] Language is plain and jargon-free
- [ ] Consistent patterns reduce learning curve

### 5. Motor Accessibility

**Accommodate different input methods:**

- [ ] Touch targets are at least 44x44px
- [ ] Clickable areas have adequate spacing
- [ ] Draggable items have keyboard alternatives
- [ ] Time-sensitive actions have adequate time
- [ ] Hovering is not required for essential info
- [ ] Double-tap/double-click not required

## Flow-Through Testing

Test complete user journeys with accessibility in mind:

    For each critical flow:
    1. Complete using ONLY keyboard
    2. Complete with eyes closed, screen reader on
    3. Complete under time pressure
    4. Complete with one hand only
    5. Note friction points for each method

## Output Format

    # Accessibility UX Review: [Feature/Flow Name]

    ## Summary
    - Keyboard-only completable: ✅/❌
    - Screen reader experience: Good/Fair/Poor
    - Cognitive load: Low/Medium/High
    - Critical issues: X

    ## Keyboard Navigation Assessment

    ### Tab Order
    [Screen diagram or list showing tab sequence]

    ### Issues Found
    | Issue | Location | Severity | Recommendation |
    |-------|----------|----------|----------------|
    | Focus lost after delete | Delete dialog | Critical | Return focus to list |

    ## Screen Reader Assessment

    ### Announcement Quality
    | Element | Current Announcement | Recommended |
    |---------|---------------------|-------------|
    | Save button | "button" | "Save changes, button" |

    ### Structure
    - Heading structure: [assessment]
    - Landmark usage: [assessment]
    - Reading order: [assessment]

    ## Cognitive Assessment
    - Clear feedback: ✅/❌
    - Error recovery: ✅/❌
    - Predictable behavior: ✅/❌

    ## Recommendations (Priority Order)
    1. **[Critical fix]**: [Description]
    2. **[High fix]**: [Description]
    3. **[Medium fix]**: [Description]

## References

For detailed ARIA patterns and keyboard interaction models: See [references/a11y-patterns.md](references/a11y-patterns.md)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/unthinkmedia) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
