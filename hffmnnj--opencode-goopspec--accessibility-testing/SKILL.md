---
name: accessibility-testing
description: Systematically verify accessibility compliance through automated and manual testing. Use when this capability is needed.
metadata:
  author: hffmnnj
---

# Accessibility Testing Skill

## Purpose
Systematically verify accessibility compliance through automated and manual testing.

## Testing Approach

### Automated Testing
1. Run axe-core or similar tools on each page
2. Integrate accessibility checks into CI/CD pipeline
3. Use Lighthouse for accessibility scoring
4. Check color contrast ratios programmatically

### Manual Testing
1. Navigate using keyboard only
2. Test with screen readers (NVDA, VoiceOver, JAWS)
3. Verify focus order is logical
4. Check that dynamic content updates are announced
5. Test at 200% zoom level

### Test Categories

- **Perceivable** - Content can be perceived by all users
- **Operable** - Interface can be operated by all users
- **Understandable** - Content and operation are understandable
- **Robust** - Content works with assistive technologies

## Common Issues

- Missing alt text on images
- Poor color contrast
- Missing form labels
- Inaccessible custom controls
- Focus traps in modals
- Missing skip links

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hffmnnj) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
