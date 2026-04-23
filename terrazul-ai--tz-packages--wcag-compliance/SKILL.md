---
name: wcag-compliance
description: Deep WCAG 2.1 criterion-by-criterion accessibility evaluation Use when this capability is needed.
metadata:
  author: terrazul-ai
---

# WCAG Compliance Skill

Perform thorough WCAG 2.1 accessibility evaluation with criterion-by-criterion testing, compliance documentation, and remediation guidance.

## When to Use

- Accessibility compliance audits
- ADA/Section 508 requirements
- Legal compliance documentation
- Inclusive design validation
- Pre-launch accessibility review
- Accessibility remediation tracking

## Capabilities

1. **Criterion-by-criterion testing** - Every applicable WCAG criterion
2. **Compliance documentation** - For legal/regulatory purposes
3. **Assistive technology testing** - Keyboard, screen reader simulation
4. **Remediation guidance** - Specific code fixes
5. **Progress tracking** - For ongoing compliance efforts

## WCAG 2.1 Conformance Levels

### Level A (Minimum)
25 criteria - Essential accessibility, basic barrier removal.

### Level AA (Standard)
13 additional criteria - Recommended target for most websites.

### Level AAA (Enhanced)
23 additional criteria - Maximum accessibility, not always achievable.

## Workflow

### Phase 1: Scope Definition

Clarify:
- Target conformance level (A, AA, AAA)
- Pages/components to test
- Applicable criteria (some may not apply)
- Timeline requirements
- Documentation needs

### Phase 2: Automated Testing

Use Puppeteer to check:
- Color contrast ratios
- Image alt text presence
- Form label associations
- Heading structure
- Link text clarity
- ARIA attribute validity
- Focus indicator presence

### Phase 3: Manual Testing

Perform hands-on testing:
- Full keyboard navigation
- Screen reader simulation
- Zoom to 200%
- Text spacing override
- Motion/animation review
- Time-based content

### Phase 4: Criterion Documentation

For each WCAG criterion, document:

```markdown
### [X.X.X Criterion Name] - Level [A/AA/AAA]

**Status**: Pass / Fail / Not Applicable

**Conformance Requirement**: [Official WCAG text]

**Testing Method**: [How tested]

**Evidence**:
- [Screenshot/observation]

**Issues Found** (if any):
1. [Location]: [Issue description]
   - Impact: [Who affected]
   - Fix: [Code example]

**Notes**: [Additional context]
```

### Phase 5: Compliance Report

Generate formal compliance documentation.

## Output Format

```markdown
# WCAG 2.1 Compliance Report

**Organization**: [Name]
**Website**: [URL]
**Conformance Target**: Level [A/AA/AAA]
**Report Date**: [Date]
**Review Period**: [Date range]

## Conformance Statement

[Organization] is committed to ensuring digital accessibility for people with disabilities. We have assessed [website] against WCAG 2.1 Level [AA] criteria.

### Conformance Status

| Level | Criteria | Passed | Failed | N/A |
|-------|----------|--------|--------|-----|
| A | 25 | X | X | X |
| AA | 13 | X | X | X |
| AAA | 23 | X | X | X |

**Overall Conformance**: [Fully Conformant / Partially Conformant / Non-Conformant]

## Executive Summary

### Strengths
- [What works well]

### Areas for Improvement
- [What needs work]

### Critical Issues
1. [Critical issue 1]
2. [Critical issue 2]

## Detailed Criteria Assessment

### Principle 1: Perceivable

#### 1.1 Text Alternatives

##### 1.1.1 Non-text Content (A)
**Status**: [Pass/Fail/N/A]
[Details...]

[Continue for each criterion...]

### Principle 2: Operable
[Criteria...]

### Principle 3: Understandable
[Criteria...]

### Principle 4: Robust
[Criteria...]

## Issues Summary

### By Severity

| Severity | Count | Impact |
|----------|-------|--------|
| Critical | X | Blocks access |
| Serious | X | Major barrier |
| Moderate | X | Inconvenience |
| Minor | X | Enhancement |

### By Principle

| Principle | Issues |
|-----------|--------|
| Perceivable | X |
| Operable | X |
| Understandable | X |
| Robust | X |

## Remediation Plan

### Immediate (1-2 weeks)
| Issue | Criterion | Priority | Effort |
|-------|-----------|----------|--------|
| [Issue] | [X.X.X] | Critical | Low |

### Short-term (1 month)
[Table...]

### Long-term (3+ months)
[Table...]

## Testing Methodology

### Tools Used
- Puppeteer for automated testing
- Manual keyboard testing
- Color contrast analysis
- ARIA validation

### Pages Tested
1. [URL 1]
2. [URL 2]

### Testing Environment
- Browser: [Browser/version]
- Screen size: [Dimensions]
- Assistive technology: [If used]

## Certification

This accessibility audit was conducted by [Name/Organization] on [Date].

**Auditor**: [Name]
**Credentials**: [If applicable]
**Contact**: [Email]

---

*This report represents the accessibility status at the time of testing. Website updates may affect conformance.*
```

## Quick Reference

### Most Common Level AA Failures

1. **1.4.3 Contrast** - Text contrast below 4.5:1
2. **2.4.7 Focus Visible** - No visible focus indicator
3. **1.1.1 Non-text Content** - Missing alt text
4. **1.3.1 Info and Relationships** - Missing form labels
5. **4.1.2 Name, Role, Value** - Improper ARIA usage

### Pass/Fail Criteria

- **Pass**: Fully meets criterion, no issues
- **Fail**: One or more violations found
- **N/A**: Criterion doesn't apply (e.g., no video = 1.2.x N/A)

## Example Usage

```
Use the wcag-compliance skill to audit [URL] at Level AA.
Document all criteria for compliance reporting.
Focus on forms and navigation components.
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/terrazul-ai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
