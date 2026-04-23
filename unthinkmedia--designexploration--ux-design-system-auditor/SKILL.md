---
name: ux-design-system-auditor
description: Compare UI implementations against design system guidelines to identify deviations in spacing, typography, colors, component usage, and interaction patterns. Use when auditing for design system compliance, checking brand consistency, reviewing component implementations, or when user mentions "design system", "component audit", "brand compliance", "Fluent", "Material Design", or "style guide check". Use when this capability is needed.
metadata:
  author: unthinkmedia
---

# UX Design System Auditor

Audit UI implementations for design system compliance and consistency.

## Audit Categories

### 1. Spacing & Layout
Check adherence to spacing scale and grid system.

### 2. Typography
Verify font families, sizes, weights, line heights.

### 3. Color Usage
Confirm semantic color application and contrast.

### 4. Component Usage
Validate correct component selection and configuration.

### 5. Iconography
Check icon style, sizing, and context appropriateness.

### 6. Interaction Patterns
Verify standard behaviors and states.

## Audit Process

1. **Identify the design system** being used (Fluent, Material, custom)
2. **Catalog elements** visible in the UI
3. **Compare against specifications** for each element
4. **Document deviations** with severity and fix guidance
5. **Summarize compliance** score and priorities

## Common Design System References

### Microsoft Fluent 2
- Spacing: 4px base unit (4, 8, 12, 16, 20, 24, 32, 40, 48)
- Typography: Segoe UI Variable, specific size ramp
- Colors: Semantic tokens (colorBrandBackground, colorNeutralForeground1, etc.)
- Border radius: 4px default, 8px for cards

### Material Design 3
- Spacing: 4px baseline grid
- Typography: Roboto with defined type scale
- Colors: Dynamic color with tonal palettes
- Shape: Rounded corners per component type

### Custom Design Systems
Document the source of truth:
- Figma library location
- Documentation URL
- Token file references

## Spacing Audit

### Checklist

| Element | Check | Pass/Fail |
|---------|-------|-----------|
| Component internal padding | Matches token spec | |
| Between related elements | Uses correct spacing token | |
| Section separation | Appropriate scale jump | |
| Page margins | Consistent across views | |
| Touch targets | Minimum 44x44 (mobile) | |

### Common Violations

| Violation | Example | Fix |
|-----------|---------|-----|
| Magic numbers | padding: 13px | Use spacing token (12 or 16) |
| Inconsistent gaps | 16px here, 20px there | Standardize to single token |
| Missing spacing | Elements touching | Add appropriate gap |
| Excessive spacing | Wasted screen space | Tighten to smaller token |

## Typography Audit

### Checklist

| Element | Check | Pass/Fail |
|---------|-------|-----------|
| Font family | Matches system font | |
| Heading hierarchy | H1 > H2 > H3 sizes correct | |
| Body text | Size and line-height match | |
| Caption/helper text | Correct diminutive size | |
| Font weights | Using approved weights only | |

### Common Violations

| Violation | Example | Fix |
|-----------|---------|-----|
| Wrong font | Arial instead of Segoe | Apply correct font-family |
| Non-standard size | font-size: 15px | Use type scale (14 or 16) |
| Missing weight | font-weight: 500 (not in scale) | Use 400, 600, or 700 |
| Poor line-height | Cramped or too loose | Apply typography token |

## Color Audit

### Checklist

| Element | Check | Pass/Fail |
|---------|-------|-----------|
| Brand colors | Using correct tokens | |
| Semantic usage | Error=red, success=green | |
| Text contrast | Meets WCAG AA (4.5:1) | |
| Interactive states | Proper hover/active colors | |
| Dark mode support | Tokens switch correctly | |

### Common Violations

| Violation | Example | Fix |
|-----------|---------|-----|
| Hardcoded colors | color: #0078d4 | Use colorBrandBackground token |
| Wrong semantic | Red for non-error decoration | Use neutral or brand color |
| Low contrast | Light gray on white | Darken text or use different bg |
| Inconsistent status | Success is green here, blue there | Standardize status colors |

## Component Audit

### Checklist

| Component | Correct Usage | Pass/Fail |
|-----------|---------------|-----------|
| Buttons | Primary for main action, secondary for others | |
| Inputs | Proper field/label association | |
| Cards | Consistent structure (header, body, actions) | |
| Modals | Standard close behavior, focus trap | |
| Tables | Using Data Grid for sortable content | |
| Navigation | Correct component for context (tabs, nav, menu) | |

### Common Violations

| Violation | Example | Fix |
|-----------|---------|-----|
| Wrong component | Link styled as button | Use Button component |
| Modified component | Custom hover color | Use component props/appearance |
| Missing states | No disabled styling | Add disabled state handling |
| Inconsistent variants | Mix of appearance types | Standardize to design specs |

## Output Format

```
# Design System Audit: [Screen/Feature Name]

## Design System Reference
- System: [Fluent 2 / Material 3 / Custom]
- Documentation: [URL]
- Version: [If applicable]

## Compliance Summary

| Category | Compliant | Deviations | Score |
|----------|-----------|------------|-------|
| Spacing | X elements | Y issues | Z% |
| Typography | X elements | Y issues | Z% |
| Color | X elements | Y issues | Z% |
| Components | X elements | Y issues | Z% |
| **Overall** | | | **Z%** |

## Deviations Found

### Spacing Issues

| Location | Current | Expected | Severity |
|----------|---------|----------|----------|
| [Element] | 13px | 12px (spacingM) | Minor |
| [Element] | No gap | 16px (spacingL) | Major |

### Typography Issues

| Location | Current | Expected | Severity |
|----------|---------|----------|----------|
| [Element] | 15px Arial | 14px Segoe UI | Major |

### Color Issues

| Location | Current | Expected | Severity |
|----------|---------|----------|----------|
| [Element] | #0078d4 | colorBrandBackground | Minor |

### Component Issues

| Location | Current | Expected | Severity |
|----------|---------|----------|----------|
| [Element] | Custom button | Button appearance="primary" | Major |

## Recommendations

### High Priority (Brand/UX Impact)
1. [Fix] - Affects: [Impact]

### Medium Priority (Consistency)
1. [Fix] - Affects: [Impact]

### Low Priority (Polish)
1. [Fix] - Affects: [Impact]

## Token Mapping Reference

[Include relevant token mappings for the specific violations found]
```

## Quick Checklist by Design System

### Fluent 2 Quick Check
- [ ] Using @fluentui/react-components
- [ ] Wrapped in FluentProvider
- [ ] Using token-based spacing
- [ ] Buttons use appearance prop correctly
- [ ] Icons from @fluentui/react-icons

### Material 3 Quick Check
- [ ] Using @mui/material v5+
- [ ] Theme provider configured
- [ ] Using sx prop or styled() for customization
- [ ] Components use variant prop correctly
- [ ] Using Material Icons

## References

For design system specifications: See [references/design-system-specs.md](references/design-system-specs.md)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/unthinkmedia) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
