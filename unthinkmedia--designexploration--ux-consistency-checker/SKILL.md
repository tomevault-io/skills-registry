---
name: ux-consistency-checker
description: Identify design inconsistencies across UI screens including visual patterns, terminology, interaction behaviors, and component usage. Use when comparing multiple screens, auditing design system compliance, checking for pattern drift, or when user mentions "consistency", "design audit", "pattern library", "style guide", or "design system compliance". Use when this capability is needed.
metadata:
  author: unthinkmedia
---

# UX Consistency Checker

Detect inconsistencies in visual design, terminology, and interaction patterns across screens.

## Consistency Categories

### 1. Visual Consistency
Same visual treatment for same functional elements.

**Check across screens:**
- Button styles (primary, secondary, destructive)
- Color usage for status (success, warning, error)
- Typography hierarchy
- Spacing and alignment
- Icon style and sizing
- Border radius and shadows

### 2. Terminological Consistency
Same words for same concepts.

**Common violations:**
| Screen A | Screen B | Issue |
|----------|----------|-------|
| "Delete" | "Remove" | Different verbs for same action |
| "Settings" | "Preferences" | Different nouns for same destination |
| "Submit" | "Save" | Unclear which commits data |
| "Cancel" | "Close" | Different exit behaviors implied |

### 3. Behavioral Consistency
Same interactions produce same results.

**Check:**
- Click behavior (single vs double)
- Hover states and reveals
- Keyboard shortcut assignments
- Gesture patterns (swipe, pinch)
- Confirmation dialog triggers

### 4. Structural Consistency
Same layout patterns for same content types.

**Check:**
- List item structure
- Card layouts
- Form field arrangements
- Modal compositions
- Error state presentations

## Audit Checklist

### Buttons
- [ ] Primary action style consistent
- [ ] Secondary action style consistent
- [ ] Destructive action style consistent (usually red)
- [ ] Disabled state consistent
- [ ] Loading state consistent
- [ ] Button placement (left vs right) consistent

### Typography
- [ ] Heading levels used consistently
- [ ] Body text sizes consistent
- [ ] Link styling consistent
- [ ] Caption/helper text consistent
- [ ] Error text consistent

### Icons
- [ ] Same icon set throughout
- [ ] Icon+label vs icon-only usage consistent
- [ ] Icon sizes consistent per context
- [ ] Icon colors follow consistent meaning

### Forms
- [ ] Label placement consistent (above, beside, floating)
- [ ] Required field indication consistent
- [ ] Validation timing consistent
- [ ] Error display consistent
- [ ] Field widths follow consistent rules

### Navigation
- [ ] Active state indication consistent
- [ ] Breadcrumb format consistent
- [ ] Back/close button placement consistent
- [ ] Menu trigger style consistent

### Feedback
- [ ] Success message style consistent
- [ ] Error message style consistent
- [ ] Loading indicator style consistent
- [ ] Empty state style consistent
- [ ] Progress indicator style consistent

## Output Format

    # Consistency Audit: [Product/Feature Name]

    ## Summary
    - Screens audited: X
    - Inconsistencies found: X
    - Critical: X | Major: X | Minor: X

    ## Findings

    ### Visual Inconsistencies
    | Element | Screen A | Screen B | Impact |
    |---------|----------|----------|--------|
    | Primary button | Blue, rounded | Blue, square | Minor - visual polish |
    | Error color | #ff0000 | #cc0000 | Minor - brand cohesion |

    ### Terminological Inconsistencies
    | Concept | Variations Found | Recommended Standard |
    |---------|-----------------|---------------------|
    | Remove item | "Delete", "Remove", "Trash" | Use "Delete" consistently |

    ### Behavioral Inconsistencies
    | Interaction | Screen A | Screen B | Recommendation |
    |------------|----------|----------|----------------|
    | Delete confirmation | Shows dialog | Immediate | Always confirm destructive actions |

    ### Structural Inconsistencies
    | Pattern | Screen A | Screen B | Recommendation |
    |---------|----------|----------|----------------|
    | List actions | Right-aligned | Inline badges | Standardize to right-aligned |

    ## Recommendations
    1. **[Priority fix]**: [Description]
    2. **[Next fix]**: [Description]

## Severity Guidelines

| Severity | Impact | Examples |
|----------|--------|----------|
| Critical | Causes user confusion, errors | "Save" vs "Submit" with different behaviors |
| Major | Noticeably unprofessional | Different icon styles across screens |
| Minor | Only designers notice | Slightly different spacing values |

## References

For consistency checklist templates: See [references/consistency-matrices.md](references/consistency-matrices.md)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/unthinkmedia) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
