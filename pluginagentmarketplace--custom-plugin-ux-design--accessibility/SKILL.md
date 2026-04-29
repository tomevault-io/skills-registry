---
name: accessibility
description: Master accessibility - WCAG compliance, screen readers, keyboard navigation, inclusive design Use when this capability is needed.
metadata:
  author: pluginagentmarketplace
---

# Accessibility Skill

> **Atomic Skill**: Ensure digital experiences are usable by everyone through WCAG compliance

## Purpose

This skill provides comprehensive guidance on accessibility standards, testing, and remediation.

## Skill Invocation

```
Skill("custom-plugin-ux-design:accessibility")
```

## Parameter Schema

### Input Parameters
```typescript
interface AccessibilityParams {
  // Required
  task: "audit" | "remediate" | "design" | "test";
  target: string;

  // Optional
  level?: "A" | "AA" | "AAA";
  scope?: "component" | "page" | "flow" | "application";
  platform?: "web" | "mobile" | "desktop";
  assistive_tech?: string[];
}
```

### Validation Rules
```yaml
task:
  type: enum
  required: true
  values: [audit, remediate, design, test]

target:
  type: string
  required: true
  min_length: 3

level:
  type: enum
  default: "AA"
  values: [A, AA, AAA]
```

## Execution Flow

```
ACCESSIBILITY EXECUTION
────────────────────────────────────────────

Step 1: SCOPE ASSESSMENT
├── Identify target scope
├── Determine compliance level
└── Select testing methods

Step 2: AUDIT
├── Automated testing
├── Manual testing
├── Assistive tech testing
└── Document findings

Step 3: CATEGORIZE ISSUES
├── Critical (blockers)
├── Serious (major barriers)
├── Moderate (difficulties)
└── Minor (inconveniences)

Step 4: REMEDIATE
├── Prioritize by severity
├── Implement fixes
├── Verify corrections
└── Document solutions

Step 5: VALIDATE
├── Re-test all issues
├── Certify compliance
└── Create maintenance plan

────────────────────────────────────────────
```

## Retry Logic

```yaml
retry_config:
  max_attempts: 3
  backoff_type: exponential
  initial_delay_ms: 500
  max_delay_ms: 5000
  retryable_errors:
    - SCANNER_TIMEOUT
    - AT_CONNECTION_LOST
```

## Logging Hooks

```typescript
interface AccessibilityLog {
  timestamp: string;
  event: "audit_start" | "issue_found" | "remediated" | "verified";
  wcag_criterion: string;
  severity: string;
  element: string;
  status: "open" | "fixed" | "verified";
}
```

## Learning Modules

### Module 1: WCAG Fundamentals
```
POUR PRINCIPLES
├── Perceivable
│   ├── Text alternatives
│   ├── Time-based media
│   ├── Adaptable content
│   └── Distinguishable
├── Operable
│   ├── Keyboard accessible
│   ├── Enough time
│   ├── Seizure safe
│   └── Navigable
├── Understandable
│   ├── Readable
│   ├── Predictable
│   └── Input assistance
└── Robust
    ├── Compatible
    └── Name, role, value
```

### Module 2: Screen Reader Support
```
SCREEN READER BASICS
├── Semantic HTML
│   ├── Headings (h1-h6)
│   ├── Landmarks (nav, main, etc.)
│   ├── Lists (ul, ol, dl)
│   └── Tables (proper markup)
├── ARIA
│   ├── Roles
│   ├── States
│   └── Properties
├── Focus management
│   ├── Focus order
│   ├── Focus trapping
│   └── Focus restoration
└── Live regions
    ├── aria-live
    ├── Announcements
    └── Status updates
```

### Module 3: Keyboard Navigation
```
KEYBOARD REQUIREMENTS
├── All interactive elements focusable
├── Logical focus order
├── Visible focus indicators
├── No keyboard traps
├── Skip links provided
└── Shortcuts documented

COMMON PATTERNS
├── Tab: Move forward
├── Shift+Tab: Move backward
├── Enter/Space: Activate
├── Arrow keys: Navigate within
├── Escape: Close/cancel
└── Home/End: First/last
```

### Module 4: Visual Accessibility
```
COLOR & CONTRAST
├── Text contrast (4.5:1 normal, 3:1 large)
├── UI component contrast (3:1)
├── Focus indicator contrast (3:1)
├── Color not sole indicator
└── Dark mode considerations

MOTION & ANIMATION
├── Reduce motion preference
├── Pause/stop controls
├── No autoplay
├── Seizure-safe content
└── Animation alternatives
```

### Module 5: Testing Methods
```
AUTOMATED TESTING
├── Axe DevTools
├── WAVE
├── Lighthouse
├── Pa11y
└── HTML validators

MANUAL TESTING
├── Keyboard-only navigation
├── Zoom to 200%
├── High contrast mode
├── Color blindness simulation
└── Form error states

ASSISTIVE TECH TESTING
├── NVDA (Windows)
├── JAWS (Windows)
├── VoiceOver (Mac/iOS)
├── TalkBack (Android)
└── Switch access
```

## Error Handling

| Error Code | Description | Recovery |
|------------|-------------|----------|
| `A11Y-001` | Missing alt text | Add descriptive alt |
| `A11Y-002` | Contrast failure | Adjust colors |
| `A11Y-003` | Keyboard trap | Add escape mechanism |
| `A11Y-004` | Missing label | Add visible/hidden label |
| `A11Y-005` | Invalid ARIA | Fix ARIA usage |

## WCAG Quick Reference

### Contrast Requirements
| Content Type | AA Ratio | AAA Ratio |
|--------------|----------|-----------|
| Normal text | 4.5:1 | 7:1 |
| Large text (18pt+) | 3:1 | 4.5:1 |
| UI components | 3:1 | 3:1 |
| Focus indicators | 3:1 | 3:1 |

### Touch Target Sizes
| Platform | Minimum | Recommended |
|----------|---------|-------------|
| Web | 24x24px | 44x44px |
| iOS | 44x44pt | 44x44pt |
| Android | 48x48dp | 48x48dp |

## Troubleshooting

### Problem: Screen reader announces wrong content
```
Diagnosis:
├── Check: ARIA labels
├── Check: DOM order
├── Check: Hidden content
└── Solution: Align ARIA with visual

Steps:
1. Test with screen reader
2. Check aria-label accuracy
3. Verify DOM = visual order
4. Remove incorrect aria-hidden
```

### Problem: Focus not visible
```
Diagnosis:
├── Check: outline: none in CSS
├── Check: :focus styles
├── Check: Custom components
└── Solution: Add visible focus

Steps:
1. Remove outline removal
2. Add :focus-visible styles
3. Ensure 3:1 contrast
4. Test all interactive elements
```

## Unit Test Templates

```typescript
describe("AccessibilitySkill", () => {
  describe("audit", () => {
    it("should detect missing alt text", async () => {
      const result = await invoke({
        task: "audit",
        target: "<img src='photo.jpg'>"
      });
      expect(result.issues).toContainEqual(
        expect.objectContaining({ code: "A11Y-001" })
      );
    });
  });

  describe("contrast checking", () => {
    it("should calculate contrast ratio", async () => {
      const result = await invoke({
        task: "audit",
        target: { text: "#666666", background: "#ffffff" }
      });
      expect(result.contrast_ratio).toBeCloseTo(5.74, 1);
    });
  });

  describe("remediation", () => {
    it("should suggest fix for contrast failure", async () => {
      const result = await invoke({
        task: "remediate",
        target: { issue: "A11Y-002", colors: ["#999", "#fff"] }
      });
      expect(result.suggested_color).toMatch(/^#[0-9A-F]{6}$/i);
      expect(result.new_contrast).toBeGreaterThanOrEqual(4.5);
    });
  });
});
```

## Quality Metrics

| Metric | Target | Measurement |
|--------|--------|-------------|
| WCAG compliance | > 95% | Criteria met |
| Critical issues | 0 | Blockers found |
| Keyboard navigable | 100% | All elements |
| Screen reader compatible | > 95% | AT testing |

## Version History

| Version | Date | Changes |
|---------|------|---------|
| 1.0.0 | 2025-12-30 | Production-grade upgrade |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/pluginagentmarketplace) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
