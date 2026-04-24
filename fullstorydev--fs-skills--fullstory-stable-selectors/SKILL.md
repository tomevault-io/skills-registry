---
name: fullstory-stable-selectors
description: Platform-agnostic guide for implementing stable, semantic identifiers in web and mobile applications. Solves the dynamic identifier problem across all platforms using the Fullstory Data Attributes Guide convention (data-component, data-id, data-section, data-position, data-state). Presents two approaches — Framework (shared components auto-decorate) and Individual Element Decoration — and instructs agents to ask users which they prefer. Core concepts apply universally; see SKILL-WEB.md for web implementation and SKILL-MOBILE.md for iOS, Android, Flutter, and React Native patterns. Use when this capability is needed.
metadata:
  author: fullstorydev
---

# Fullstory Stable Selectors

> **📱 Platform-Specific Implementation**: This document covers core concepts. For implementation details, see:
> - **Web (JavaScript/TypeScript)**: [SKILL-WEB.md](./SKILL-WEB.md) — React, Vue, Angular, Svelte, Next.js, and more
> - **Mobile**: [SKILL-MOBILE.md](./SKILL-MOBILE.md) — iOS, Android, Flutter, React Native

---

## Overview

Modern applications—both web and mobile—often have **dynamic, unpredictable element identifiers** that change across builds, deployments, or even at runtime. This creates challenges for:

1. **Fullstory**: Reliable search, defined elements, heatmaps
2. **Automated Testing**: Stable E2E test selectors
3. **Computer User Agents (CUA)**: AI agents navigating your interface
4. **Accessibility Tools**: Programmatic element identification

**The Solution**: Add stable, semantic identifiers that describe **what** the element is, not how it's rendered.

This skill follows the conventions established in the Fullstory Data Attributes Guide, which recommends five core attributes: `data-component`, `data-id`, `data-section`, `data-position`, and `data-state`.

---

## The Universal Problem

### Web: Dynamic CSS Classes

```html
<!-- What your code looks like -->
<button className={styles.primaryButton}>Add to Cart</button>

<!-- What renders in the browser -->
<button class="Button_primaryButton__x7Ks2">Add to Cart</button>
                                    ↑
                        This hash changes every build!
```

### Mobile: Dynamic View IDs

```
iOS View Hierarchy:
UIButton (0x7f8b4c0123a0)    ← Memory address changes every launch
  └── "Add to Cart"

Android View Tree:
Button (id: view-12345)       ← Auto-generated, unstable
  └── "Add to Cart"

React Native Bridge:
ReactButton (nativeID: rn_7)  ← Bridge-generated, changes on re-render
```

### Impact Across Platforms

| Tool | Web Problem | Mobile Problem |
|------|-------------|----------------|
| **Fullstory** | CSS selectors break | View tree queries unreliable |
| **E2E Testing** | Cypress/Playwright tests brittle | Detox/Espresso tests break |
| **AI Agents (CUA)** | Cannot find elements | Cannot navigate reliably |
| **Automation** | Scripts fail on deploy | Scripts fail on app update |

---

## Why This Matters for AI Agents (CUA)

Computer User Agents—AI systems that interact with digital interfaces—rely on stable, semantic identifiers to understand and navigate your application.

```
┌─────────────────────────────────────────────────────────────────────────┐
│  HOW CUAs "SEE" YOUR INTERFACE                                          │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                         │
│  ❌ BRITTLE (AI struggles):                                             │
│                                                                         │
│     Web: <button class="sc-3d8f2a btn_primary__xK7n2">                 │
│     iOS: UIButton at memory 0x7f8b4c0123a0                             │
│     Android: view-12345                                                 │
│                                                                         │
│  ✅ SEMANTIC (AI understands):                                          │
│                                                                         │
│     Web: data-component="button" data-id="add-to-cart"                 │
│     iOS: accessibilityIdentifier = "button.add-to-cart"                │
│     Android: testTag = "button.add-to-cart"                            │
│                                                                         │
│  The AI can now reliably:                                               │
│  • Find "the add-to-cart button"                                       │
│  • Understand the component type (button) and specific instance        │
│  • Maintain stable automation across deployments                        │
└─────────────────────────────────────────────────────────────────────────┘
```

**Stable selectors provide CUAs with:**
- ✅ Consistent element identification across builds
- ✅ Semantic understanding of element purpose
- ✅ Hierarchical context (section → component → instance)
- ✅ State awareness for interaction planning

---

## The Universal Solution

Add stable identifiers that survive build changes and runtime variations:

| Platform | Stable Identifier Mechanism |
|----------|----------------------------|
| **Web** | `data-component`, `data-id`, `data-section`, `data-position`, `data-state` attributes |
| **iOS** | `accessibilityIdentifier` property |
| **Android (Kotlin)** | `contentDescription` or resource ID |
| **Android (Compose)** | `testTag` modifier, `semantics` |
| **React Native** | `testID` prop |
| **Flutter** | `Key`, `Semantics` widget |

**The naming conventions are the same across all platforms** — only the implementation mechanism differs. For mobile apps, these attributes can also be set programmatically with `FS.setAttribute` calls.

---

## Core Concepts

### The Attribute Set

Based on the Fullstory Data Attributes Guide, five core attributes form the stable selector vocabulary:

| Attribute | Purpose | Example Values |
|-----------|---------|----------------|
| **`data-component`** | Identifies the **type** of component — a card, button, accordion, carousel, link, row, etc. Should be paired with `data-id` to identify the specific instance. | `card`, `button`, `accordion`, `carousel`, `link`, `row`, `notification-bar` |
| **`data-id`** | Identifies the **specific instance** of a component. Should be unique within a given `data-section` or nested `data-component`. | `wireless-headphones`, `add-to-cart`, `Get Demo`, `shipping-method` |
| **`data-section`** | Identifies a **major section** of the page — header, footer, sidebar, hero. If a section is really just a specific instance of a reusable component, use `data-component` + `data-id` instead. | `header`, `footer`, `navigation`, `additional-resources`, `hero` |
| **`data-position`** | Identifies the **position** of a repeated element within a list. Only needed for repeating elements where position might be useful for analytics. | `1`, `2`, `3` |
| **`data-state`** | Identifies the current **state** a component is in. Can also be used for counters or values. | `checked`, `unchecked`, `expanded`, `collapsed`, `loading`, `active` |

> **Note**: There is nothing magical about these exact attribute names. Some companies add `analytics` as part of the attribute (e.g., `data-analytics-id`) to make it clearer these are for analytics usage. You can choose different names if that works for your team. **It is the concept of the attributes that is important, not their final names.**

### Semantic Hierarchy

```
┌─────────────────────────────────────────────────────────────────────────┐
│  SEMANTIC HIERARCHY (applies to all platforms)                          │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                         │
│  Section: "checkout"                              ← Page area           │
│  │                                                                      │
│  ├── Component: "form" / ID: "shipping"           ← Component + ID     │
│  │   ├── ID: "address-input"                      ← Interactive element │
│  │   └── ID: "city-input"                                               │
│  │                                                                      │
│  ├── Component: "form" / ID: "payment"                                  │
│  │   └── ID: "card-input"                                               │
│  │                                                                      │
│  └── Component: "button" / ID: "place-order"                            │
│      State: "enabled|disabled|loading"            ← Current state       │
│                                                                         │
└─────────────────────────────────────────────────────────────────────────┘
```

### Platform Implementation Quick Reference

| Concept | Web | iOS | Android | React Native | Flutter |
|---------|-----|-----|---------|--------------|---------|
| Component type | `data-component="card"` | `accessibilityIdentifier = "card"` | `testTag("card")` | `testID="card"` | `Key("card")` |
| Instance ID | `data-id="wireless-headphones"` | `.wireless-headphones` suffix | `.wireless-headphones` suffix | `.wireless-headphones` suffix | `.wireless-headphones` suffix |
| Combined | `data-component="card"` + `data-id="wireless-headphones"` | `"card.wireless-headphones"` | `"card.wireless-headphones"` | `"card.wireless-headphones"` | `Key("card.wireless-headphones")` |
| Section | `data-section="header"` | _(use as prefix)_ | _(use as prefix)_ | _(use as prefix)_ | _(use as prefix)_ |
| Position | `data-position="1"` | _(append to ID)_ | _(append to ID)_ | _(append to ID)_ | _(append to ID)_ |

---

## Two Approaches to Decoration

There are two primary strategies for applying data attributes. **Agents should ask users which approach fits their team before proceeding with implementation.**

### Approach A: Framework Approach (Recommended for Design Systems)

Build data attributes into your **shared, reusable components**. When every team uses a shared `FullstoryButton`, that component automatically emits `data-component="button"` and `data-id={i18nKey}` — no manual work needed by consuming developers.

```
┌─────────────────────────────────────────────────────────────────────────┐
│  FRAMEWORK APPROACH                                                      │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                         │
│  Shared Component Library                                               │
│  ┌──────────────────────────────┐                                       │
│  │ <FullstoryButton             │                                       │
│  │   i18nKey="checkout.submit"> │                                       │
│  │   Place Order                │                                       │
│  │ </FullstoryButton>           │                                       │
│  └──────────────────────────────┘                                       │
│                   │                                                      │
│                   ▼  Automatically emits:                                │
│  ┌──────────────────────────────────────────────┐                       │
│  │ <button                                       │                      │
│  │   data-component="button"                     │                      │
│  │   data-id="checkout.submit">                  │                      │
│  │   Place Order                                 │                      │
│  │ </button>                                     │                      │
│  └──────────────────────────────────────────────┘                       │
│                                                                         │
│  ✅ Benefits:                                                           │
│  • Zero effort for consuming developers                                 │
│  • Consistent naming across the entire app                              │
│  • Scales automatically as the app grows                                │
│  • Resilient to refactors — component handles the decoration            │
│  • i18n keys are already stable, human-readable identifiers             │
│                                                                         │
│  ⚠️ Considerations:                                                     │
│  • Requires investment in a shared component library                    │
│  • May not cover custom one-off elements                                │
│  • Less granular control for individual elements                        │
│                                                                         │
│  Best for: Teams with established design systems or component libraries │
│                                                                         │
└─────────────────────────────────────────────────────────────────────────┘
```

### Approach B: Individual Element Decoration

Developers manually add data attributes to individual elements in their templates. This approach works with any codebase immediately.

```
┌─────────────────────────────────────────────────────────────────────────┐
│  INDIVIDUAL ELEMENT DECORATION                                           │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                         │
│  Developer adds attributes directly:                                    │
│  ┌──────────────────────────────────────────────┐                       │
│  │ <button                                       │                      │
│  │   data-component="button"                     │                      │
│  │   data-id="place-order"                       │                      │
│  │   className={styles.primary}>                 │                      │
│  │   Place Order                                 │                      │
│  │ </button>                                     │                      │
│  └──────────────────────────────────────────────┘                       │
│                                                                         │
│  ✅ Benefits:                                                           │
│  • Works with any codebase immediately                                  │
│  • No shared component library dependency                               │
│  • Full control over what gets decorated                                │
│  • PascalCase code names can be used (test automation friendly)         │
│                                                                         │
│  ⚠️ Considerations:                                                     │
│  • Manual effort per element                                            │
│  • Risk of naming inconsistency across developers                       │
│  • Requires team discipline and code review                             │
│  • May be forgotten on new elements                                     │
│                                                                         │
│  Best for: Teams without a shared component library, or teams wanting   │
│  immediate results                                                      │
│                                                                         │
└─────────────────────────────────────────────────────────────────────────┘
```

> **Agent Directive**: When a developer asks for help with data attributes or stable selectors, **always ask which approach they prefer** before generating code. Present the benefits and considerations of each approach. Many teams use a hybrid — Framework for shared components (buttons, cards, inputs) and Individual for custom one-off elements.

---

## Naming Conventions (Universal)

These naming conventions apply to **all platforms**.

### Formal Naming Grammar

```
┌─────────────────────────────────────────────────────────────────────────┐
│  NAMING GRAMMAR                                                          │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                         │
│  data-component: <type>                                                 │
│                                                                         │
│    The TYPE of component. Use lowercase or kebab-case.                  │
│    Examples:                                                            │
│    • card             (content card)                                    │
│    • button           (interactive button)                              │
│    • accordion        (expandable content)                              │
│    • carousel         (sliding content)                                 │
│    • link             (navigation link)                                 │
│    • row              (table or list row)                               │
│    • notification-bar (compound component type)                         │
│    • stackable-elements (compound component type)                      │
│                                                                         │
│    Alternative: PascalCase code component names (ProductCard,           │
│    CheckoutForm) are valid for teams that prefer developer-centric      │
│    naming, especially when aligning with test automation selectors.     │
│                                                                         │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                         │
│  data-id: <instance-identifier>                                         │
│                                                                         │
│    The SPECIFIC instance. Should be unique within its parent            │
│    data-section or nested data-component. Use descriptive,              │
│    human-readable names.                                                │
│    Examples:                                                            │
│    • wireless-headphones  (product name)                                │
│    • add-to-cart          (button purpose)                              │
│    • Get Demo             (CTA label)                                   │
│    • shipping-method      (form element purpose)                        │
│    • checkout.submit      (i18n key, used in Framework approach)        │
│                                                                         │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                         │
│  data-section: <page-area>                                              │
│                                                                         │
│    A MAJOR section of the page. Use kebab-case.                         │
│    Examples:                                                            │
│    • header                                                             │
│    • footer                                                             │
│    • navigation                                                         │
│    • hero                                                               │
│    • additional-resources                                               │
│    • behavioral-data                                                    │
│                                                                         │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                         │
│  data-position: <number>                                                │
│                                                                         │
│    Position in a repeated list. Use integers starting at 1.             │
│    Examples: 1, 2, 3                                                    │
│                                                                         │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                         │
│  data-state: <current-state>                                            │
│                                                                         │
│    The current state of the component. Use kebab-case.                  │
│    Examples:                                                            │
│    • checked / unchecked                                                │
│    • expanded / collapsed                                               │
│    • loading / loaded / error                                           │
│    • active / inactive                                                  │
│    • Can also be a counter value (e.g., "5" for a like count)          │
│                                                                         │
└─────────────────────────────────────────────────────────────────────────┘
```

### Component Type Names (data-component)

**Recommended**: Use **lowercase or kebab-case** describing the type of component:

```
✅ RECOMMENDED: Generic component types
• card
• button
• accordion
• carousel
• link
• row
• notification-bar
• stackable-elements

✅ ALSO VALID: PascalCase code component names (for test automation alignment)
• ProductCard
• CheckoutForm
• NavigationHeader

❌ BAD: Too generic or meaningless
• Container
• Wrapper
• Component
• View
• div
```

### Instance Identifiers (data-id)

Use **descriptive, human-readable names** that identify the specific instance:

```
✅ GOOD: Describes what this specific instance IS
• wireless-headphones    (product name)
• add-to-cart            (button purpose)
• Get Demo              (CTA label)
• shipping-method        (form element purpose)
• 6 hurdles of data     (content title)

✅ GOOD: Qualified names for disambiguation
• billing-address-line1
• shipping-address-line1

❌ BAD: Describes appearance or position
• blue-button
• big-button
• button-1
• first-item
• left-sidebar
```

### Section Names (data-section)

Use **kebab-case** describing the page area:

```
✅ GOOD: Clear page areas
• header
• footer
• navigation
• hero
• additional-resources
• behavioral-data
• site-header

❌ BAD: Too specific (use data-component + data-id instead)
• product-card-section     (this is a component, not a section)
• button-area              (too granular for a section)
```

---

## What to Annotate

### Always Annotate

- ✅ **Buttons and tappable elements** — Primary interaction points
- ✅ **Form inputs** — Text fields, selects, checkboxes, toggles
- ✅ **Links and navigation items** — Navigation paths
- ✅ **Cards and list items** — Items in repeating content (use `data-position`)
- ✅ **Modals and dialog triggers** — State-changing interactions
- ✅ **Tab and accordion controls** — Content switchers (use `data-state`)
- ✅ **Major page sections** — Header, footer, navigation (use `data-section`)

### Skip Annotation For

- ❌ **Pure layout containers** — Unless interactive
- ❌ **Styling wrappers** — Divs/Views for styling only
- ❌ **Text-only elements** — Unless key analytics content

---

## Best Practices

### 1. Annotate at Development Time

Add identifiers as you write components, not as an afterthought. This ensures complete coverage.

### 2. Document Your Conventions

Create a team style guide covering:
- Component type naming patterns (lowercase types vs PascalCase code names)
- Instance identifier patterns
- Section boundaries
- Required annotations
- Platform-specific implementation

### 3. Combine with Privacy Controls

Stable identifiers and privacy controls work together:
- Annotate sensitive elements for searchability
- Apply privacy masking/exclusion for data protection
- Both serve complementary purposes

### 4. Use data-section for Page Structure, data-component for Reusable Parts

```
✅ GOOD: Sections for page areas, components for reusable elements
data-section="additional-resources"
  └── data-component="card" data-id="behavioral data"
  └── data-component="card" data-id="6 hurdles of data"

❌ BAD: Everything is a component
data-component="AdditionalResourcesSection"   ← Should be a section
  └── data-component="ResourceCard"
```

### 5. Use Business Identifiers, Not Positions

For lists and repeating content, use `data-id` with a business identifier and `data-position` for the slot:

```
✅ GOOD: Business identifier + position
data-component="card" data-id="wireless-headphones" data-position="1"

❌ BAD: Position-based identification only
data-id="item-0", data-id="item-1", data-id="item-2"
```

### 6. Pair data-component with data-id

A `data-component` should generally be paired with a `data-id` that represents the specific instance:

```
✅ GOOD: Component type + specific instance
<div data-component="card" data-id="wireless-headphones">

❌ BAD: Component without instance identification
<div data-component="card">  ← Which card?
```

---

## Integration with Accessibility

Stable selectors complement accessibility attributes:

| Attribute Type | Purpose | Audience |
|----------------|---------|----------|
| **Stable identifiers** | Programmatic targeting | Fullstory, Tests, AI Agents |
| **Accessibility labels** | Human-readable description | Screen readers, AI understanding |
| **Semantic roles** | Element type/behavior | Accessibility, AI categorization |

**Best Practice**: Use BOTH stable identifiers AND accessibility attributes. They serve complementary purposes.

---

## Troubleshooting

### Identifiers Not Working

**Common issues across all platforms:**
1. Identifier not set on the element (verify in inspector/debugger)
2. Typos in identifier names
3. Build tools stripping identifiers in production
4. Conditional rendering removing elements

### Too Many Search Results

**Problem:** Searching for `[data-component="button"]` returns hundreds of results

**Solution:** Be more specific with hierarchical identifiers:
- Use `[data-section="checkout"] [data-id="place-order"]`
- Combine section + component + id for unique targeting
- Use `[data-component="card"][data-id="wireless-headphones"]` for specificity

### Identifiers Stripped in Production

**Check platform-specific build configurations:**
- Web: Verify `data-*` attributes aren't removed by minifiers
- Mobile: Ensure debug-only code isn't stripping identifiers

---

## KEY TAKEAWAYS FOR AGENT

When helping developers implement stable selectors:

### Step 0: Ask Which Approach

**Before generating any code, ask the developer:**

> "Would you like to build data attributes into shared components (**Framework approach** — recommended for teams with design systems) or add them to individual elements directly (**Individual Element approach** — works with any codebase)?"

Present the benefits and considerations of each approach (see "Two Approaches to Decoration" section above). Many teams use a **hybrid** — Framework for shared components and Individual for custom elements.

### Step 1: Detect Platform and Route

1. **Detect platform first** — Web vs iOS vs Android vs React Native vs Flutter
2. **Route to implementation file** — SKILL-WEB.md or SKILL-MOBILE.md
3. **Use consistent naming** — Same attribute concepts apply to all platforms

### Core Principles (All Platforms)

1. **Name by purpose, not appearance**: `data-id="add-to-cart"` not `data-id="blue-button"`
2. **Use the five attributes**: `data-component` (type), `data-id` (instance), `data-section` (area), `data-position` (slot), `data-state` (state)
3. **Pair component with id**: A `data-component` should have a `data-id`
4. **Use sections for page areas**: `data-section="header"` not `data-component="header"`
5. **Annotate interactive elements**: Buttons, inputs, links, cards in lists
6. **Combine with accessibility**: Stable IDs + ARIA/accessibility labels
7. **Business IDs, not positions**: `data-id="wireless-headphones"` not `data-id="item-0"`

### Questions to Ask Developers

1. "What platform(s) are you building for?" (Web, iOS, Android, React Native, Flutter)
2. **"Would you prefer the Framework approach (shared components auto-decorate) or Individual Element decoration?"**
3. "Do you have a shared component library or design system?"
4. "Are your element identifiers stable across builds?"
5. "What elements do you need to reliably find in Fullstory?"
6. "Are you using E2E testing tools?"
7. "Is AI/automation tooling on your roadmap?"

### Implementation Checklist (All Platforms)

```markdown
Phase 1: Core Implementation
□ Choose approach (Framework vs Individual vs Hybrid)
□ Identify interactive elements that need tracking
□ Establish naming convention (component types + instance identifiers)
□ Add data-section to major page areas (header, footer, nav)
□ Add data-component + data-id to reusable components
□ Add data-position to repeated/listed elements
□ Add data-state to stateful elements
□ Verify identifiers survive production build
□ Test in Fullstory search

Phase 2: AI/CUA Readiness
□ Ensure data-state is set for stateful elements (expandable, toggleable)
□ Ensure accessibility attributes complement stable IDs
□ Document naming conventions for team consistency

Phase 3: Enterprise Scale
□ If Framework approach: build attributes into shared component library
□ Implement type-safe identifier helpers
□ Add namespace prefixes for modules/teams (micro-frontends)
□ Configure E2E tools to use data-id or data-component selectors
```

---

## REFERENCE LINKS

### Fullstory Documentation
- **Data Attributes Guide**: Fullstory Data Attributes Guide (internal reference)
- **Element Properties**: ../core/fullstory-element-properties/SKILL.md
- **Privacy Controls**: ../core/fullstory-privacy-controls/SKILL.md
- **Test Automation**: ./fullstory-test-automation/SKILL.md

### Platform-Specific Implementation
- **Web Implementation**: [SKILL-WEB.md](./SKILL-WEB.md)
- **Mobile Implementation**: [SKILL-MOBILE.md](./SKILL-MOBILE.md)

### Accessibility Standards
- **WAI-ARIA Authoring Practices**: https://www.w3.org/WAI/ARIA/apg/
- **iOS Accessibility**: https://developer.apple.com/accessibility/
- **Android Accessibility**: https://developer.android.com/guide/topics/ui/accessibility

---

*This skill provides the universal foundation for stable selectors across all platforms. See SKILL-WEB.md for web implementation and SKILL-MOBILE.md for mobile implementation.*

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/fullstorydev) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
