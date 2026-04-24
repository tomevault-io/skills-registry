---
name: fullstory-getting-started
description: > Use when this capability is needed.
metadata:
  author: fullstorydev
---

# Fullstory Getting Started Guide

> **🎯 START HERE**: This is the definitive entry point for Fullstory implementation. If you're unsure where to begin, you're in the right place.

> **🤖 AGENT NOTE**: Do NOT load all skills into context. If the user's request is unclear, **ask what they want to focus on** before loading additional skills. See "Key Takeaways for Agent" section below for loading strategy.

---

## How Core Skills Work: SKILL.md First

Every core skill follows a **three-file pattern**. You always start with **SKILL.md** for concepts, then go to the platform-specific file for implementation code.

```
┌─────────────────────────────────────────────────────────────────────────────┐
│  CORE SKILL FILE STRUCTURE                                                   │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  📖 SKILL.md (START HERE)                                                   │
│  ├── Platform-agnostic concepts                                             │
│  ├── API reference and parameters                                           │
│  ├── Best practices and anti-patterns                                       │
│  ├── When to use this API                                                   │
│  └── Links to SKILL-WEB.md and SKILL-MOBILE.md                             │
│                                                                             │
│           ┌─────────────────┴─────────────────┐                             │
│           ▼                                   ▼                             │
│                                                                             │
│  🌐 SKILL-WEB.md                       📱 SKILL-MOBILE.md                   │
│  JavaScript/TypeScript                 iOS (Swift/SwiftUI)                  │
│  React, Vue, Angular                   Android (Kotlin/Compose)             │
│  Next.js, Svelte, etc.                 Flutter (Dart)                       │
│  Implementation code                   React Native                         │
│  Framework patterns                    Implementation code                  │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

### Example: Using the Analytics Events Skill

1. **Read `fullstory-analytics-events/SKILL.md`** — Learn event naming, property types, rate limits, best practices
2. **Then read the implementation file for your platform:**
   - Web → `SKILL-WEB.md` for JavaScript/TypeScript code
   - Mobile → `SKILL-MOBILE.md` for iOS/Android/Flutter/React Native code

### Mobile Sequencing: Which Skills, In What Order?

For mobile implementations, the **mobile-instrumentation-orchestrator** helps you understand which skills to implement in what sequence:

```
Privacy Controls → User Identification → Page Properties → Analytics Events → Logging
```

The orchestrator doesn't replace reading SKILL.md files — it tells you **which SKILL.md files to read and when**, then routes to the appropriate SKILL-MOBILE.md sections for implementation.

---

## Understanding the Skill Architecture

The Fullstory Skills Repository (FSR) is organized in layers. Understanding this architecture helps you find the right guidance quickly.

```
┌─────────────────────────────────────────────────────────────────────────────┐
│  FULLSTORY SKILLS ARCHITECTURE (Start at Top, Work Down)                     │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  ┌───────────────────────────────────────────────────────────────────────┐ │
│  │  CORE SKILLS (API Foundation) ← START HERE                            │ │
│  │  fullstory-identify-users, fullstory-analytics-events, etc.           │ │
│  │  → Complete API reference and implementation guidance                 │ │
│  │  → SKILL.md: Platform-agnostic concepts and best practices            │ │
│  │  → SKILL-WEB.md: JavaScript/TypeScript implementation                 │ │
│  │  → SKILL-MOBILE.md: iOS, Android, Flutter, React Native               │ │
│  └───────────────────────────────────────────────────────────────────────┘ │
│                                    │                                        │
│                                    ▼ orchestrated by                        │
│  ┌───────────────────────────────────────────────────────────────────────┐ │
│  │  META SKILLS (Strategy Layer)                                          │ │
│  │  fullstory-getting-started (YOU ARE HERE)                             │ │
│  │  fullstory-privacy-strategy, universal-data-scoping-and-decoration    │ │
│  │  mobile-instrumentation-orchestrator                                  │ │
│  │  → Expert guidance on using core APIs together effectively            │ │
│  │  → Decision frameworks: what data, where, when                        │ │
│  │  → Implementation sequencing (which core skills in what order)        │ │
│  └───────────────────────────────────────────────────────────────────────┘ │
│                                    │                                        │
│                                    ▼ guided by                              │
│  ┌───────────────────────────────────────────────────────────────────────┐ │
│  │  INDUSTRY SKILLS (Domain Expert Layer)                                 │ │
│  │  fullstory-banking, fullstory-ecommerce, fullstory-healthcare, etc.   │ │
│  │  → Industry-specific privacy requirements and regulations             │ │
│  │  → Domain-specific data capture patterns                              │ │
│  │  → Compliance guidance (HIPAA, PCI, GLBA, GDPR)                       │ │
│  └───────────────────────────────────────────────────────────────────────┘ │
│                                    │                                        │
│                                    ▼ enhanced by                            │
│  ┌───────────────────────────────────────────────────────────────────────┐ │
│  │  FRAMEWORK SKILLS (Enhancement Layer)                                  │ │
│  │  fullstory-stable-selectors, fullstory-test-automation                │ │
│  │  → Cross-platform tooling: stable identifiers, test generation        │ │
│  │  → Optional enhancements for automation and testing readiness         │ │
│  └───────────────────────────────────────────────────────────────────────┘ │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

### Skill Layer Reference (Top to Bottom)

| Layer | Purpose | When to Use | Entry Point |
|-------|---------|-------------|-------------|
| **1. Core** | Complete Fullstory API expertise across web and mobile | Need to implement a specific API (identify, events, properties, etc.) | **Always SKILL.md first**, then SKILL-WEB.md or SKILL-MOBILE.md |
| **2. Meta** | Expert guidance on using APIs together | Planning implementation, deciding where data goes, sequencing | SKILL.md (orchestrates which core skills to use) |
| **3. Industry** | Domain-specific compliance and patterns | Working in regulated industries or need vertical-specific guidance | SKILL.md (references core skills for implementation) |
| **4. Framework** | Cross-platform tooling and automation | Need stable selectors for testing/AI, want test automation | SKILL.md, then SKILL-WEB.md or SKILL-MOBILE.md |

### Core Skills: The API Foundation

Each core skill provides **complete guidance** for one Fullstory API. **Always start with SKILL.md** for concepts, then read the platform-specific implementation file.

| Core Skill | API | Start Here | Then Implementation |
|------------|-----|------------|---------------------|
| `fullstory-identify-users` | User identification | **SKILL.md** | SKILL-WEB.md or SKILL-MOBILE.md |
| `fullstory-anonymize-users` | Session anonymization | **SKILL.md** | SKILL-WEB.md or SKILL-MOBILE.md |
| `fullstory-user-properties` | User attributes | **SKILL.md** | SKILL-WEB.md or SKILL-MOBILE.md |
| `fullstory-page-properties` | Page/screen context | **SKILL.md** | SKILL-WEB.md or SKILL-MOBILE.md |
| `fullstory-element-properties` | Interaction context | **SKILL.md** | SKILL-WEB.md, SKILL-IOS.md, SKILL-ANDROID.md, etc. |
| `fullstory-analytics-events` | Custom events | **SKILL.md** | SKILL-WEB.md or SKILL-MOBILE.md |
| `fullstory-privacy-controls` | Mask/exclude elements | **SKILL.md** | SKILL-WEB.md or SKILL-MOBILE.md |
| `fullstory-user-consent` | GDPR/CCPA consent | **SKILL.md** | SKILL-WEB.md or SKILL-MOBILE.md |
| `fullstory-capture-control` | Pause/resume | **SKILL.md** | SKILL-WEB.md or SKILL-MOBILE.md |
| `fullstory-observe-callbacks` | Lifecycle hooks | **SKILL.md** | SKILL-WEB.md or SKILL-MOBILE.md |
| `fullstory-logging` | Session logging | **SKILL.md** | SKILL-WEB.md or SKILL-MOBILE.md |
| `fullstory-async-methods` | Promise-based APIs | **SKILL.md** | SKILL-WEB.md or SKILL-MOBILE.md |

### Meta Skills: Expert Orchestration

Meta skills don't replace core skills — they help you **use core skills effectively**.

| Meta Skill | Purpose |
|------------|---------|
| `fullstory-getting-started` | **Entry point** — skill architecture, navigation, web quick start |
| `mobile-instrumentation-orchestrator` | **Mobile sequencing** — which core skills to implement and in what order (routes to SKILL-MOBILE.md files) |
| `fullstory-privacy-strategy` | **Privacy expert** — what to capture, mask, exclude, hash (works with `fullstory-privacy-controls`) |
| `universal-data-scoping-and-decoration` | **Data placement expert** — user vs page vs element vs event (works with property skills) |

### Industry Skills: Domain Expertise

| Industry Skill | Regulations | Key Concerns |
|----------------|-------------|--------------|
| `fullstory-banking` | PCI, GLBA, SOX | Financial data exclusion, amount ranges |
| `fullstory-healthcare` | HIPAA, HITECH | PHI exclusion, BAA requirements |
| `fullstory-ecommerce` | PCI, CCPA, GDPR | Conversion tracking, payment exclusion |
| `fullstory-gaming` | Gaming licenses, AML | Responsible gaming, wager exclusion |
| `fullstory-saas` | SOC 2, GDPR | Feature adoption, customer data |
| `fullstory-travel` | PCI, GDPR | Booking flows, ID exclusion |
| `fullstory-media-entertainment` | COPPA, GDPR | Engagement, children's privacy |

### Framework Skills: Enhancement Tooling

| Framework Skill | Purpose |
|-----------------|---------|
| `fullstory-stable-selectors` | Stable identifiers using the Data Attributes Guide convention (data-component, data-id, data-section, data-position, data-state). Supports Framework approach (shared components) and Individual Element decoration — **agents should ask users which approach they prefer** |
| `fullstory-test-automation` | Generate test automation from decorated codebases |

---

## How to Navigate: Common Questions

| Question | Go To | Then |
|----------|-------|------|
| "How do I implement **user identification**?" | `fullstory-identify-users/SKILL.md` | SKILL-WEB.md or SKILL-MOBILE.md |
| "How do I **track events**?" | `fullstory-analytics-events/SKILL.md` | SKILL-WEB.md or SKILL-MOBILE.md |
| "How do I **protect sensitive data**?" | `fullstory-privacy-controls/SKILL.md` | SKILL-WEB.md or SKILL-MOBILE.md |
| "What data should I **capture vs mask vs exclude**?" | `fullstory-privacy-strategy/SKILL.md` | (strategy, then core skills) |
| "Should this be a **user property, page property, or event**?" | `universal-data-scoping-and-decoration/SKILL.md` | (strategy, then core skills) |
| "What **order** should I implement mobile APIs?" | `mobile-instrumentation-orchestrator/SKILL.md` | (sequencing, routes to SKILL-MOBILE.md) |
| "I'm in **healthcare/banking/etc** — what rules apply?" | `fullstory-{industry}/SKILL.md` | (guidance, references core skills) |
| "How do I add **stable selectors** for testing/AI?" | `fullstory-stable-selectors/SKILL.md` | SKILL-WEB.md or SKILL-MOBILE.md |
| "How do I **generate tests** from my decorated codebase?" | `fullstory-test-automation/SKILL.md` | SKILL-WEB.md or SKILL-MOBILE.md |

---

## Mobile SDK Installation

For mobile SDK installation and initial setup, Fullstory provides comprehensive official guides. **Use these for SDK setup**, then return to the core skills' **SKILL-MOBILE.md** files for API usage guidance.

### Official Installation Guides by Platform

| Platform | Installation Guide | What It Covers |
|----------|-------------------|----------------|
| **Android (Kotlin)** | [Getting Started with Android Data Capture](https://help.fullstory.com/hc/en-us/articles/360040596093-Getting-Started-with-Android-Data-Capture) | Comprehensive Android setup: Gradle, initialization, ProGuard, configuration |
| **+ Jetpack Compose** | [Getting Started with Jetpack Compose](https://help.fullstory.com/hc/en-us/articles/15666387415575-Getting-Started-with-Jetpack-Compose) | Additional Compose-specific setup (use with Android guide above) |
| **iOS (UIKit/Swift)** | [Getting Started with iOS Data Capture](https://help.fullstory.com/hc/en-us/articles/360042772333-Getting-Started-with-iOS-Data-Capture) | Comprehensive iOS setup: CocoaPods/SPM, initialization, Info.plist |
| **+ SwiftUI** | [Integrating Fullstory into a SwiftUI App](https://help.fullstory.com/hc/en-us/articles/8867138701719-Integrating-Fullstory-into-a-SwiftUI-App) | Additional SwiftUI-specific setup (use with iOS guide above) |
| **Flutter** | [Getting Started with Flutter Mobile Apps](https://help.fullstory.com/hc/en-us/articles/27461129353239-Getting-Started-with-Fullstory-for-Flutter-Mobile-Apps) | pub.dev setup, Dart initialization |
| **React Native** | [Getting Started with React Native Capture](https://help.fullstory.com/hc/en-us/articles/360052419133-Getting-Started-with-Fullstory-React-Native-Capture) | npm/yarn setup, native linking |
| **Cordova/Capacitor** | [Getting Started with Cordova or Capacitor](https://help.fullstory.com/hc/en-us/articles/16924219219223-Getting-Started-with-Cordova-or-Capacitor-Data-Capture) | Plugin installation, hybrid app setup |

### Android: Agent-assisted setup

For **Android**, you can use the in-repo setup guide so the AI companion can apply the changes for you:

- **File**: [SKILL-ANDROID.md](./SKILL-ANDROID.md) (in this folder)
- **Flow**: The agent will **prompt you for your Fullstory Org ID**, then insert the correct snippets into your project’s `project/build.gradle` (or `settings.gradle`), `app/build.gradle`, and initialization code as needed.
- **Use this when**: You want copy-paste-free setup and are starting a new Android integration.

After the agent applies the edits, continue with the core skills’ **SKILL-MOBILE.md** files for API usage (identify, events, properties, privacy).

### iOS: Agent-assisted setup

For **iOS**, you can use the in-repo setup guide so the AI companion can apply the changes for you:

- **File**: [SKILL-IOS.md](./SKILL-IOS.md) (in this folder)
- **Flow**: The agent will **prompt you for your Fullstory Org ID**, check if the project uses CocoaPods or Swift Package Manager, then insert the correct pod/package, add the Build Phase script, and configure `Info.plist`.
- **Use this when**: You want copy-paste-free setup and are starting a new iOS integration.

After the agent applies the edits, continue with the core skills' **SKILL-MOBILE.md** files for API usage (identify, events, properties, privacy).

### Flutter: Agent-assisted setup

For **Flutter**, you can use the in-repo setup guide so the AI companion can apply the changes for you:

- **File**: [SKILL-FLUTTER.md](./SKILL-FLUTTER.md) (in this folder)
- **Flow**: The agent will **prompt you for your Fullstory Org ID**, apply Android native Gradle setup, run `flutter pub add fullstory_flutter`, then modify your Dart entry-point (`runApp` → `runFullstoryApp`, etc.) with the required import.
- **Use this when**: You want copy-paste-free setup and are starting a new Flutter integration.

After the agent applies the edits, continue with the core skills' **SKILL-FLUTTER.md** files for API usage (identify, events, properties, privacy).

### React Native: Agent-assisted setup

For **React Native**, you can use the in-repo setup guide so the AI companion can apply the changes for you:

- **File**: [SKILL-REACT-NATIVE.md](./SKILL-REACT-NATIVE.md) (in this folder)
- **Flow**: The agent will **prompt you for your Fullstory Org ID**, then guide through npm/yarn installation, iOS pod install, Info.plist configuration, and Android Gradle setup.
- **Use this when**: You want copy-paste-free setup and are starting a new React Native integration.

After the agent applies the edits, continue with the core skills' **SKILL-REACT-NATIVE.md** files for API usage (identify, events, properties, privacy).

### After SDK Installation

Once the SDK is installed and capturing data:

1. **Read the core skill SKILL.md** for the API you need (e.g., `fullstory-identify-users/SKILL.md`)
2. **Then read SKILL-MOBILE.md** for platform-specific implementation code
3. **Use `mobile-instrumentation-orchestrator/SKILL.md`** for recommended implementation order

> **Note**: The official guides above cover SDK installation. The skills repository covers **API usage** — how to identify users, track events, set properties, handle privacy, etc.

---

## Web Quick Start Guide

> **Note**: This section provides a web quick start overview. For complete API guidance, always read the relevant **core skill's SKILL.md first**, then its **SKILL-WEB.md** for detailed implementation code.

---

## The Fullstory Web API Ecosystem

### Complete API Reference (Browser v2)

| API | Method | Purpose | Core Skill |
|-----|--------|---------|------------|
| **Identity** | `FS('setIdentity', { uid, properties })` | Identify known users | `fullstory-identify-users` |
| **Anonymize** | `FS('setIdentity', { anonymous: true })` | End identified session | `fullstory-anonymize-users` |
| **User Properties** | `FS('setProperties', { type: 'user', ... })` | Update user attributes | `fullstory-user-properties` |
| **Page Properties** | `FS('setProperties', { type: 'page', ... })` | Set page context | `fullstory-page-properties` |
| **Element Properties** | `data-fs-*` attributes | Capture interaction context | `fullstory-element-properties` |
| **Events** | `FS('trackEvent', { name, properties })` | Track discrete actions | `fullstory-analytics-events` |
| **Observers** | `FS('observe', { type, callback })` | React to FS lifecycle | `fullstory-observe-callbacks` |
| **Logging** | `FS('log', { level, msg })` | Add logs to session | `fullstory-logging` |
| **Consent** | `FS('setIdentity', { consent: bool })` | Control capture consent | `fullstory-user-consent` |
| **Capture Control** | `FS('shutdown')` / `FS('restart')` | Pause/resume capture | `fullstory-capture-control` |
| **Async Methods** | `FS('*Async')` variants | Promise-based API calls | `fullstory-async-methods` |

### API Categories

```
┌─────────────────────────────────────────────────────────────────────┐
│                        FULLSTORY BROWSER API v2                      │
├─────────────────────────────────────────────────────────────────────┤
│  IDENTITY & USER                                                     │
│  ├── setIdentity (uid)        - Identify users                      │
│  ├── setIdentity (anonymous)  - Anonymize users                     │
│  ├── setIdentity (consent)    - Manage consent                      │
│  └── setProperties (user)     - Update user attributes              │
├─────────────────────────────────────────────────────────────────────┤
│  CONTEXT & PROPERTIES                                                │
│  ├── setProperties (page)     - Page-level context                  │
│  └── Element Properties       - Interaction-level context           │
├─────────────────────────────────────────────────────────────────────┤
│  EVENTS & ACTIONS                                                    │
│  └── trackEvent               - Discrete business events            │
├─────────────────────────────────────────────────────────────────────┤
│  PRIVACY & CONTROL                                                   │
│  ├── fs-exclude / fs-mask     - Element privacy classes             │
│  ├── shutdown / restart       - Capture control                     │
│  └── consent                  - User consent management             │
├─────────────────────────────────────────────────────────────────────┤
│  LIFECYCLE & UTILITIES                                               │
│  ├── observe                  - Lifecycle callbacks                 │
│  ├── log                      - Session logging                     │
│  ├── getSession               - Get session URL                     │
│  └── *Async variants          - Promise-based methods               │
└─────────────────────────────────────────────────────────────────────┘
```

---

## Web Snippet Installation

For snippet installation and configuration, see the official Fullstory documentation:

**[Getting Started with Web](https://developer.fullstory.com/browser/getting-started/)** — Comprehensive guide covering:
- Snippet installation (HTML, npm `@fullstory/browser`, Google Tag Manager, Segment, Tealium)
- Configuration options (`_fs_org`, `_fs_namespace`, `_fs_capture_on_startup`)
- Browser SDK usage

### After Snippet Installation

Once the snippet is installed and capturing data:

1. **Read the core skill SKILL.md** for the API you need (e.g., `fullstory-identify-users/SKILL.md`)
2. **Then read SKILL-WEB.md** for JavaScript/TypeScript implementation code
3. **Use the API reference below** to find the right skill for your use case

> **Note**: The official guide covers snippet installation. The skills repository covers **API usage** — how to identify users, track events, set properties, handle privacy, etc.

---

## Web Implementation Order

### Recommended Implementation Sequence

```
Phase 1: Foundation
├── 1. Install snippet
├── 2. Implement privacy controls (fs-exclude, fs-mask)
├── 3. Implement user identification (login flow)
├── 4. Implement anonymization (logout flow)
└── 5. Set up basic page properties (pageName)

Phase 2: Context
├── 6. Add user properties (plan, role, company)
├── 7. Enhance page properties (search terms, filters)
└── 8. Add element properties (product IDs, positions)

Phase 3: Events
├── 9. Implement key business events
├── 10. Add funnel tracking
└── 11. Add feature usage tracking

Phase 4: Advanced
├── 12. Set up observers for session URL
├── 13. Implement consent flows (if GDPR required)
├── 14. Add error logging
└── 15. Implement capture control (if needed)

Phase 5: Enhancement (Optional)
├── 16. Add stable selectors (fullstory-stable-selectors)
└── 17. Generate test automation (fullstory-test-automation)
```

### Minimum Viable Implementation

For a basic integration, implement these APIs:

```javascript
// 1. Identify users on login
FS('setIdentity', {
  uid: user.id,
  properties: {
    displayName: user.name,
    email: user.email
  }
});

// 2. Set page context
FS('setProperties', {
  type: 'page',
  properties: {
    pageName: 'Dashboard'
  }
});

// 3. Track key events
FS('trackEvent', {
  name: 'Feature Used',
  properties: {
    featureName: 'export'
  }
});

// 4. Anonymize on logout
FS('setIdentity', { anonymous: true });
```

---

## Privacy First: Three Privacy Modes

Before capturing any data, understand the three privacy modes:

### Privacy Mode Comparison

| Mode | CSS Class | What Leaves Device | Events Captured | Use For |
|------|-----------|-------------------|-----------------|---------|
| **Exclude** | `.fs-exclude` | ❌ Nothing | ❌ No | Passwords, SSN, PHI, card numbers |
| **Mask** | `.fs-mask` | ⚠️ Structure only | ✅ Yes | Names, emails, addresses |
| **Unmask** | `.fs-unmask` | ✅ Everything | ✅ Yes | Public content, products |

### Quick Privacy Decision

```
Is this data regulated (HIPAA, PCI, GLBA)?
     │
     YES → fs-exclude (nothing leaves device)
     │
     NO
     │
     ▼
Is this PII (name, email, address)?
     │
     YES → fs-mask (structure captured, text replaced)
     │
     NO
     │
     ▼
Is this public/business data?
     │
     YES → fs-unmask or default (everything captured)
```

### Privacy by Industry Quick Reference

| Industry | Default Recommendation | Industry Skill |
|----------|----------------------|----------------|
| Healthcare | Use Private by Default mode; explicit unmask only | `fullstory-healthcare` |
| Banking | Exclude financial data; mask PII | `fullstory-banking` |
| Gaming | Exclude amounts; mask user info | `fullstory-gaming` |
| E-commerce | Unmask products; mask checkout PII; exclude payment | `fullstory-ecommerce` |
| SaaS | Unmask features; mask user content | `fullstory-saas` |
| Travel | Unmask search/booking; mask traveler PII; exclude IDs | `fullstory-travel` |
| Media | Unmask content; mask profiles | `fullstory-media-entertainment` |

---

## Quick Decision Guide

### "Where should I put this data?"

```
Is this data about WHO the user is?
(plan, role, company, signup date)
     │
     YES → User Properties (setIdentity or setProperties type: 'user')
     │
     NO
     │
     ▼
Is this data the same for the entire page?
(search term, product ID on detail page, filters)
     │
     YES → Page Properties (setProperties type: 'page')
     │
     NO
     │
     ▼
Is this data specific to one element among many?
(product ID in a grid, position in list)
     │
     YES → Element Properties (data-fs-* attributes)
     │
     NO
     │
     ▼
Is this a discrete action/event?
(purchase, signup, feature used)
     │
     YES → Event (trackEvent)
```

> **Deep Dive**: See `universal-data-scoping-and-decoration/SKILL.md` for comprehensive guidance.

### Quick Reference Table

| I want to... | Use this API | Core Skill |
|--------------|--------------|------------|
| Link sessions to a user | `setIdentity` | `fullstory-identify-users` |
| Add user attributes | `setProperties(user)` | `fullstory-user-properties` |
| Set page context | `setProperties(page)` | `fullstory-page-properties` |
| Track what was clicked | Element Properties | `fullstory-element-properties` |
| Track a conversion | `trackEvent` | `fullstory-analytics-events` |
| Handle logout | `setIdentity(anonymous)` | `fullstory-anonymize-users` |
| Get session URL | `observe` or `getSession` | `fullstory-observe-callbacks` |
| Implement GDPR consent | `setIdentity(consent)` | `fullstory-user-consent` |
| Pause recording | `shutdown`/`restart` | `fullstory-capture-control` |
| Log errors to session | `log` | `fullstory-logging` |
| Protect sensitive data | CSS classes | `fullstory-privacy-controls` |

---

## Industry-Specific Quick Start

### Banking/Financial Services
```javascript
// Use internal customer ID, never SSN or account numbers
FS('setIdentity', { uid: customer.customerId });

// Exclude all financial data, use ranges if needed
// Add fs-exclude to: balances, transactions, account numbers
FS('trackEvent', {
  name: 'transfer_completed',
  properties: {
    transfer_type: 'internal',
    amount_range: '$100-$500'  // Never exact amount
  }
});
```
**→ See `fullstory-banking/SKILL.md` for complete guide**

### E-commerce
```javascript
// Rich product tracking is valuable
FS('setProperties', {
  type: 'page',
  properties: {
    pageName: 'Product Detail',
    productId: product.id,
    productName: product.name,
    price: product.price  // Prices are fine in e-commerce
  }
});

// Track conversions with full details
FS('trackEvent', {
  name: 'purchase_completed',
  properties: {
    order_id: order.id,
    revenue: order.total,
    item_count: order.items.length
  }
});
```
**→ See `fullstory-ecommerce/SKILL.md` for complete guide**

### Healthcare
```javascript
// Extremely limited capture - use anonymous sessions if possible
FS('setIdentity', { uid: generateSessionId() });  // No linking to patient

// Exclude EVERYTHING medical - masking is NOT sufficient
// Use Private by Default mode
// Only track: navigation, errors, page load times
```
**→ See `fullstory-healthcare/SKILL.md` for complete guide**

### SaaS
```javascript
// Rich user identification for feature adoption
FS('setIdentity', {
  uid: user.id,
  properties: {
    displayName: user.firstName,
    plan: organization.plan,
    role: user.role,
    org_id: organization.id
  }
});

// Track feature usage comprehensively
FS('trackEvent', {
  name: 'feature_first_use',
  properties: {
    feature_name: 'report_builder',
    days_since_signup: user.daysSinceSignup
  }
});
```
**→ See `fullstory-saas/SKILL.md` for complete guide**

---

## Testing Your Integration

### 1. Verify Snippet Installation

Open browser console and check:
```javascript
typeof FS !== 'undefined'  // Should be true
FS('getSession')           // Should return session URL
```

### 2. Check Privacy Controls

In browser DevTools:
- Verify `.fs-exclude` classes on sensitive elements
- Verify `.fs-mask` classes on PII fields
- Watch a session replay and confirm masked/excluded content

### 3. Check User Identification

After login, verify in Fullstory:
- Session shows user's displayName
- User can be found by search
- User properties appear in session details

### 4. Validate Events

After triggering events:
- Check Events panel in Fullstory session
- Verify event properties are captured correctly
- Test event-based searches work

---

## Troubleshooting

### Fullstory Not Loading

```javascript
if (typeof FS === 'undefined') {
  console.error('Fullstory not loaded - check snippet installation');
}
```

**Common causes:**
- Snippet not in `<head>`
- Wrong org ID
- Ad blocker blocking script
- Content Security Policy blocking

### User Not Identified

```javascript
console.log('Identifying user:', user.id);
FS('setIdentity', { uid: user.id });
```

**Common causes:**
- uid is undefined or null
- setIdentity called before FS loads
- Identity called with wrong syntax

### Privacy Not Working

**Common causes:**
- CSS class not on element (check DevTools)
- Child element needs its own class
- Conflicting CSS selector rules in Settings

---

## Key Takeaways for Agent

When helping developers with Fullstory implementation:

### 1. Don't Load All Skills — Ask First

**Context efficiency is critical.** There are 12+ core skills, multiple meta/industry/framework skills. Loading them all would bloat the context window unnecessarily.

**If the user's intent is unclear, ASK:**
- "What are you trying to implement today?" (identity, events, privacy, etc.)
- "What platform are you building for?" (web, iOS, Android, Flutter, React Native)
- "Are you in a regulated industry?" (healthcare, banking, insurance, etc.)

**Then load only the relevant skills:**
- 1-2 core skills based on what they're implementing
- 1 industry skill if applicable
- Framework skills only if they mention testing/selectors

### 2. SKILL.md First — Always
- **Core skills**: Always read SKILL.md first for concepts, then SKILL-WEB.md or SKILL-MOBILE.md for implementation
- **Meta skills**: Provide orchestration and strategy, route TO core skills
- **Industry skills**: Provide domain guidance, reference core skills for implementation
- **Framework skills**: Same pattern — SKILL.md first, then platform-specific

### 3. Skill Architecture Summary
- **Core skills**: Complete API guidance — SKILL.md (concepts) + SKILL-WEB.md + SKILL-MOBILE.md (implementation)
- **Meta skills**: Expert orchestration — help decide WHICH core skills to use and in WHAT ORDER
- **Industry skills**: Domain-specific compliance — reference core skills for HOW to implement
- **Framework skills**: Enhancement tooling — stable selectors, test automation (same file pattern)

### 4. Privacy First
- Ask what industry/regulations apply
- Point to appropriate industry skill
- Default to more restrictive privacy in regulated industries

### 5. Implementation Order Matters
- Start with privacy controls
- Then identification (login/logout)
- Add page properties (pageName is crucial for Journeys)
- Then events and element properties

### 5b. Ask About Decoration Approach Before Implementing Stable Selectors

When a developer wants to add data attributes or stable selectors, **ask which approach they prefer before generating code**:

> "Would you like to build data attributes into shared components (**Framework approach** — recommended for teams with design systems) or add them to individual elements directly (**Individual Element approach** — works with any codebase)?"

| Approach | Best For | Benefits | Considerations |
|----------|----------|----------|----------------|
| **Framework** (shared components auto-decorate) | Teams with design systems or component libraries | Zero effort for consuming devs, consistent naming, scales automatically | Requires shared component library investment |
| **Individual Element** (manual decoration) | Any codebase, immediate results | No dependencies, full control, test automation friendly | Manual effort, risk of inconsistency |
| **Hybrid** (most teams) | Practical mix | Framework for shared components + Individual for custom elements | Good default recommendation |

This follows the Fullstory Data Attributes Guide convention with five core attributes: `data-component`, `data-id`, `data-section`, `data-position`, `data-state`. See `fullstory-stable-selectors/SKILL.md` for the full Two Approaches section.

### 6. Skill Loading Strategy

**Minimal load for common scenarios:**

| User Intent | Load These Skills Only |
|-------------|------------------------|
| "Help me identify users" | `fullstory-identify-users/SKILL.md` + platform file |
| "Help me track events" | `fullstory-analytics-events/SKILL.md` + platform file |
| "Help me with privacy" | `fullstory-privacy-controls/SKILL.md` + platform file |
| "I'm building mobile" | `mobile-instrumentation-orchestrator/SKILL.md` (it routes to others) |
| "I'm in healthcare" | `fullstory-healthcare/SKILL.md` + `fullstory-privacy-controls/SKILL.md` |
| "I need stable selectors" | `fullstory-stable-selectors/SKILL.md` + platform file |
| "I'm not sure what I need" | **Ask the user** before loading anything |

### 7. Common User Questions
| Question | Start With | Then |
|----------|------------|------|
| "How do I link sessions to users?" | `fullstory-identify-users/SKILL.md` | SKILL-WEB.md or SKILL-MOBILE.md |
| "Where should I put this data?" | `universal-data-scoping-and-decoration/SKILL.md` | (routes to appropriate core skills) |
| "How do I track conversions?" | `fullstory-analytics-events/SKILL.md` | SKILL-WEB.md or SKILL-MOBILE.md |
| "What should I mask vs exclude?" | `fullstory-privacy-controls/SKILL.md` | SKILL-WEB.md or SKILL-MOBILE.md |
| "What order for mobile?" | `mobile-instrumentation-orchestrator/SKILL.md` | (sequences which core SKILL.md → SKILL-MOBILE.md) |

### 8. Routing Pattern
For any implementation question:
1. **Start with SKILL.md** of the relevant core skill (concepts, API reference, best practices)
2. **Then platform-specific** — SKILL-WEB.md or SKILL-MOBILE.md for implementation code
3. **Add industry guidance** if in a regulated vertical (references core skills)
4. **Add framework skills** for enhancements (stable selectors, test automation)

---

## Reference Links

### Web SDK Documentation
- **Getting Started (Web)**: https://developer.fullstory.com/browser/getting-started/
- **Custom Properties**: https://developer.fullstory.com/browser/custom-properties/

### Mobile SDK Installation Guides (Help Center)
- **Android (Kotlin)**: https://help.fullstory.com/hc/en-us/articles/360040596093-Getting-Started-with-Android-Data-Capture
- **Android + Jetpack Compose**: https://help.fullstory.com/hc/en-us/articles/15666387415575-Getting-Started-with-Jetpack-Compose
- **iOS (UIKit/Swift)**: https://help.fullstory.com/hc/en-us/articles/360042772333-Getting-Started-with-iOS-Data-Capture
- **iOS + SwiftUI**: https://help.fullstory.com/hc/en-us/articles/8867138701719-Integrating-Fullstory-into-a-SwiftUI-App
- **Flutter**: https://help.fullstory.com/hc/en-us/articles/27461129353239-Getting-Started-with-Fullstory-for-Flutter-Mobile-Apps
- **React Native**: https://help.fullstory.com/hc/en-us/articles/360052419133-Getting-Started-with-Fullstory-React-Native-Capture
- **Cordova/Capacitor**: https://help.fullstory.com/hc/en-us/articles/16924219219223-Getting-Started-with-Cordova-or-Capacitor-Data-Capture

### Mobile SDK API Documentation (Developer Portal)
- **iOS API Reference**: https://developer.fullstory.com/mobile/ios/
- **Android API Reference**: https://developer.fullstory.com/mobile/android/
- **React Native API Reference**: https://developer.fullstory.com/mobile/react-native/
- **Flutter API Reference**: https://developer.fullstory.com/mobile/flutter/

### Help Center
- **Privacy Controls**: https://help.fullstory.com/hc/en-us/articles/360020623574
- **Help Center Home**: https://help.fullstory.com/

---

*This getting started guide is the definitive entry point for Fullstory implementation. For detailed API guidance, see core skills. For mobile implementation, see the mobile instrumentation orchestrator. For industry-specific requirements, see industry skills.*

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/fullstorydev) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
