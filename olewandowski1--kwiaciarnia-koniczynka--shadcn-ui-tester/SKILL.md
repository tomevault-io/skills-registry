---
name: shadcn-ui-tester
description: Uses Playwright MCP to open and test landing pages in real browsers, validating responsiveness, accessibility, navigation, and UX behavior, and applying minimal, targeted fixes when safe. Use when this capability is needed.
metadata:
  author: olewandowski1
---

# Shadcn UI Tester Skill

This skill verifies and hardens completed landing pages by opening the
application in **real browser environments using Playwright MCP via Antigravity**.

It validates real user behavior and applies small, safe fixes when issues are
clearly identified.

## Mandatory Pre-Step: Repository Analysis

Before running any browser tests or applying fixes, the tester MUST:

1. Read the repository structure
2. Understand:
   - Component organization
   - Styling and theming approach
   - Navigation patterns
3. Respect project conventions and design intent

❗ The tester must not introduce structural or stylistic deviations.

## When to Use This Skill

- The landing page implementation is complete
- UI was built using `shadcn-ui-builder`
- `npm run build` and `npm run dev` succeed
- The goal is final UX and behavior verification before delivery or deployment

## Tooling Requirement (MANDATORY)

- This skill MUST use **Playwright MCP**
- Tests MUST be executed through Antigravity-controlled Playwright browsers
- Manual reasoning without browser execution is NOT sufficient

## Core Responsibilities

This skill MUST:

1. Launch the application in real browsers using Playwright MCP
2. Interact with the UI as a real user would
3. Validate responsiveness, accessibility, and UX behavior
4. Identify bugs, regressions, or rule violations
5. Apply **minimal, targeted fixes** when safe
6. Clearly report issues that require human decisions

## What This Skill Tests

### 1. Viewport & Responsiveness

Test at minimum:
- Mobile viewport
- Tablet viewport
- Desktop viewport

Checks:
- No layout breakage
- No horizontal scrolling
- Content remains readable
- Hero section fills viewport height

### 2. Hero Section Validation

- Hero is the first visible section
- Hero occupies full viewport height (100vh)
- Primary H1 is present and visible
- No layout shift on initial load

### 3. Navigation

#### Mobile Navigation (MANDATORY)

- Mobile menu opens and closes correctly
- Menu is keyboard accessible
- Focus handling is correct
- All primary navigation items are reachable

#### Scroll-Based Navigation (Conditional)

If the page is a single-page, scroll-based layout:
- Anchor links scroll correctly
- Navigation behavior matches scroll position

### 4. Scroll-to-Top Button (Conditional)

If scroll-based navigation is present:
- Button appears after meaningful scroll depth
- Button is keyboard accessible
- Button scrolls to the top correctly
- Motion is subtle and non-blocking

### 5. Accessibility (WCAG 2.1 AA – Practical Validation)

Validate:
- Full keyboard navigation
- Visible focus indicators
- Proper label association
- Logical tab order
- No obvious ARIA misuse

This skill performs **practical validation**, not a full formal audit.

### 6. Motion & UX Safety

- Animations do not block content visibility
- No layout-shifting animations
- Reduced-motion preference does not break UX

## Fixing Rules (CRITICAL)

### Allowed Fixes ✅

The tester MAY apply:
- Missing or broken focus styles
- Incorrect tab order
- Non-functional mobile menu toggles
- Broken scroll-to-top behavior
- Small responsive layout bugs
- Clearly incorrect ARIA attributes

### Disallowed Fixes ❌

The tester MUST NOT:
- Redesign sections
- Change layout structure
- Introduce new dependencies
- Refactor components deeply
- Override branding or design intent

If an issue requires significant change:
- Report it clearly
- Do NOT fix automatically

## Reporting Format

Always produce a **Testing Summary**:

### ✅ Passed Checks
- Verified behaviors

### ⚠️ Issues Found
For each issue:
- Description
- Location
- Severity (low / medium / high)
- Fixed automatically or not

### 🔧 Applied Fixes
- What was fixed
- Why it was safe
- Files affected

### ❗ Manual Follow-Ups
- Issues requiring human decisions

## Re-Testing Rule

If fixes are applied:
- Re-run affected Playwright checks
- Confirm resolution
- Avoid unnecessary full re-runs

## Output Expectations

- Clear, structured test report
- Minimal and justified code changes
- No architectural drift
- Increased confidence in production readiness

## What NOT to Do

- Do NOT over-fix
- Do NOT guess design intent
- Do NOT hide issues
- Do NOT replace human judgment

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/olewandowski1) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
