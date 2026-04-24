---
name: mobile-instrumentation-orchestrator
description: Decision engine and implementation roadmap for sequencing Fullstory mobile SDK instrumentation. Ensures data capture follows the correct sequence (Privacy → Identity → Navigation → Interaction → Diagnostics) across iOS, Android, Flutter, and React Native. Routes to platform-specific SKILL-MOBILE.md files for implementation details. Use when this capability is needed.
metadata:
  author: fullstorydev
---

# Mobile Instrumentation Orchestrator

## Overview

This skill provides the **sequencing logic** for mobile Fullstory instrumentation. It answers:

- **When** should each API be called?
- **In what order** should instrumentation be applied?
- **Which skill** provides the implementation details?

The sequence is platform-agnostic—iOS, Android, Flutter, and React Native all follow the same logical flow. For implementation syntax, this skill routes to the appropriate `SKILL-MOBILE.md` files.

---

## The Implementation Sequence

Instrumentation must follow a specific sequence to ensure data integrity and user privacy:

```
┌─────────────────────────────────────────────────────────────────────────────┐
│  MOBILE INSTRUMENTATION SEQUENCE                                             │
├─────────────────────────────────────────────────────────────────────────────┤
│                                                                             │
│  [ STAGE 1 ]      [ STAGE 2 ]      [ STAGE 3 ]      [ STAGE 4 ]            │
│     INIT     →       AUTH     →       NAV      →      ACTION               │
│       │                │                │                │                  │
│       v                v                v                v                  │
│  ┌─────────┐     ┌─────────┐     ┌─────────┐     ┌─────────┐              │
│  │ Privacy │     │Identity │     │  Pages  │     │ Events  │              │
│  │ Consent │     │  Users  │     │  Props  │     │  Track  │              │
│  └─────────┘     └─────────┘     └─────────┘     └─────────┘              │
│       │                │                │                │                  │
│       │                │                │                v                  │
│       │                │                │          ┌─────────┐              │
│       │                │                └────────→ │ Stable  │              │
│       │                │                           │Selectors│              │
│       │                │                           └─────────┘              │
│       │                │                                │                   │
│       └────────────────┴────────────────────────────────┼──────────────→   │
│                                                         v                   │
│                                                   ┌─────────┐              │
│                                                   │ Logging │              │
│                                                   │ (Errors)│              │
│                                                   └─────────┘              │
│                                                                             │
└─────────────────────────────────────────────────────────────────────────────┘
```

---

## Decision Matrix

| Stage | Trigger | Primary Skill | Implementation File |
|-------|---------|---------------|---------------------|
| **1. Init** | App launch | Privacy Controls | `fullstory-privacy-controls/SKILL-MOBILE.md` |
| **1. Init** | GDPR/consent required | User Consent | `fullstory-user-consent/SKILL-MOBILE.md` |
| **2. Auth** | Successful login | Identify Users | `fullstory-identify-users/SKILL-MOBILE.md` |
| **2. Auth** | Logout | Anonymize Users | `fullstory-anonymize-users/SKILL-MOBILE.md` |
| **3. Nav** | Screen appears | Page Properties | `fullstory-page-properties/SKILL-MOBILE.md` |
| **3. Nav** | Screen disappears | Page Properties | `fullstory-page-properties/SKILL-MOBILE.md` |
| **4. Action** | User interaction | Analytics Events | `fullstory-analytics-events/SKILL-MOBILE.md` |
| **4. Action** | Element annotation | Stable Selectors | `fullstory-stable-selectors/SKILL-MOBILE.md` |
| **Any** | Error occurs | Logging | `fullstory-logging/SKILL-MOBILE.md` |
| **Any** | Sensitive screen | Capture Control | `fullstory-capture-control/SKILL-MOBILE.md` |

---

## Stage Details

### Stage 1: Initialization (App Launch)

**When**: App starts, before any user data is captured

**Purpose**: Establish privacy baseline and consent state

**Skills to Apply**:
1. **Privacy Controls** — Set up masking/exclusion for sensitive UI elements
2. **User Consent** — If GDPR/CCPA applies, check consent before capture
3. **Capture Control** — If needed, start in paused state

```
┌─────────────────────────────────────────────────────────────────┐
│  STAGE 1: INITIALIZATION CHECKLIST                               │
├─────────────────────────────────────────────────────────────────┤
│  □ Configure SDK initialization                                  │
│  □ Apply privacy classes to sensitive views (fs-mask, fs-exclude)│
│  □ Check consent state (if GDPR/CCPA applies)                   │
│  □ Set capture state based on consent                           │
│  □ DO NOT track events or identify users yet                    │
└─────────────────────────────────────────────────────────────────┘
```

**Implementation**: See `fullstory-privacy-controls/SKILL-MOBILE.md`

---

### Stage 2: Authentication (Login/Logout)

**When**: User authentication state changes

**Purpose**: Link sessions to known users

**Skills to Apply**:
1. **Identify Users** — On login success, call `identify` with user ID
2. **User Properties** — Set user attributes (plan, role, etc.)
3. **Anonymize Users** — On logout, end the identified session

```
┌─────────────────────────────────────────────────────────────────┐
│  STAGE 2: AUTHENTICATION CHECKLIST                               │
├─────────────────────────────────────────────────────────────────┤
│  On Login Success:                                               │
│  □ Call identify with user ID (NOT email/PII as UID)            │
│  □ Set user properties (plan, role, account_type)               │
│  □ DO NOT include PII in user properties                        │
│                                                                  │
│  On Logout:                                                      │
│  □ Call anonymize/setIdentity(anonymous: true)                  │
│  □ Clear any cached user state                                  │
└─────────────────────────────────────────────────────────────────┘
```

**Implementation**: See `fullstory-identify-users/SKILL-MOBILE.md`

---

### Stage 3: Navigation (Screen Lifecycle)

**When**: User navigates between screens

**Purpose**: Establish page/screen context for all interactions

**Skills to Apply**:
1. **Pages API** — Create Page objects and manage their lifecycle
2. **Page Properties** — Set pageName and screen-level properties
3. **Stable Selectors** — Ensure screen root has stable identifier

Unlike web applications where Pages are tied to URLs, mobile Pages are defined programmatically. The recipe:
1. Create a page object
2. Call `start` on that page object
3. Update any properties for that page
4. Call `end` on that page object (or it ends when the next page starts)

There can only be **one active Page at any time** — Pages cannot overlap or be nested. Consider making **Dialogs, Overlays, and Alerts** into Pages too, especially if they take up the full screen.

```
┌─────────────────────────────────────────────────────────────────┐
│  STAGE 3: NAVIGATION CHECKLIST                                   │
├─────────────────────────────────────────────────────────────────┤
│  On Screen Appear:                                               │
│  □ Create page object: FS.page(withName: "ScreenName")          │
│  □ Call page.start() to begin the page lifecycle                │
│  □ Set page properties (productId, searchTerm, step, etc.)      │
│  □ Ensure screen root has stable identifier (e.g., "checkout")  │
│                                                                  │
│  On Screen Disappear:                                            │
│  □ Call page.end() explicitly (best practice)                   │
│  □ Events tracked after end should be on NEW page context       │
│                                                                  │
│  For Dialogs/Overlays/Alerts:                                    │
│  □ Consider making full-screen overlays into Pages              │
│  □ Helps provide context of where the user is                   │
│  □ Enhances analytics and troubleshooting                       │
└─────────────────────────────────────────────────────────────────┘
```

**Implementation**: See `fullstory-page-properties/SKILL-MOBILE.md`

---

### Stage 4: Actions (User Interactions)

**When**: User performs meaningful actions

**Purpose**: Track business events and ensure element identification

**Skills to Apply**:
1. **Analytics Events** — Track discrete actions (purchases, signups, etc.) with `trackEvent`
2. **Stable Selectors** — Annotate interactive elements using the Data Attributes Guide convention
3. **Data Attributes** — Use `FS.setAttribute` to set `data-component`, `data-id`, `data-section`, `data-position`, `data-state` on mobile views
4. **Logging** — Track errors that occur during actions

Critical business events that should be tracked as Events (not just via stable selectors):
- Adding items to cart/bag
- Purchases
- Registrations
- A/B test exposure
- Feature usage

```
┌─────────────────────────────────────────────────────────────────┐
│  STAGE 4: ACTIONS CHECKLIST                                      │
├─────────────────────────────────────────────────────────────────┤
│  For Interactive Elements:                                       │
│  □ Add stable identifiers (accessibilityIdentifier, testID, Key)│
│  □ Use component-type.instance-id naming (e.g., "button.add-to-│
│    cart") — see fullstory-stable-selectors/SKILL-MOBILE.md      │
│  □ Use FS.setAttribute for analytics attributes (data-component,│
│    data-id, data-section, data-position, data-state)            │
│                                                                  │
│  For Business Events:                                            │
│  □ Call trackEvent with descriptive name                        │
│  □ Include event-specific properties (quantity, SKU, method)    │
│  □ Events inherit page context and user properties automatically│
│  □ Do NOT include PII in event properties                       │
│                                                                  │
│  For Error Handling:                                             │
│  □ Track errors with FS.log or custom error events              │
│  □ Include error category, NOT full error message (may have PII)│
└─────────────────────────────────────────────────────────────────┘
```

**Implementation**:
- Events: `fullstory-analytics-events/SKILL-MOBILE.md`
- Selectors: `fullstory-stable-selectors/SKILL-MOBILE.md`
- Errors: `fullstory-logging/SKILL-MOBILE.md`

---

## Platform-Specific Routing

### iOS (Swift/UIKit/SwiftUI)

| Skill | Implementation File |
|-------|---------------------|
| Privacy Controls | `fullstory-privacy-controls/SKILL-MOBILE.md` → iOS section |
| Identify Users | `fullstory-identify-users/SKILL-MOBILE.md` → iOS section |
| Page Properties | `fullstory-page-properties/SKILL-MOBILE.md` → iOS section |
| Analytics Events | `fullstory-analytics-events/SKILL-MOBILE.md` → iOS section |
| Stable Selectors | `fullstory-stable-selectors/SKILL-MOBILE.md` → iOS section |
| Logging | `fullstory-logging/SKILL-MOBILE.md` → iOS section |

### Android (Kotlin/Java/Compose)

| Skill | Implementation File |
|-------|---------------------|
| Privacy Controls | `fullstory-privacy-controls/SKILL-MOBILE.md` → Android section |
| Identify Users | `fullstory-identify-users/SKILL-MOBILE.md` → Android section |
| Page Properties | `fullstory-page-properties/SKILL-MOBILE.md` → Android section |
| Analytics Events | `fullstory-analytics-events/SKILL-MOBILE.md` → Android section |
| Stable Selectors | `fullstory-stable-selectors/SKILL-MOBILE.md` → Android section |
| Logging | `fullstory-logging/SKILL-MOBILE.md` → Android section |

### Flutter

| Skill | Implementation File |
|-------|---------------------|
| Privacy Controls | `fullstory-privacy-controls/SKILL-MOBILE.md` → Flutter section |
| Identify Users | `fullstory-identify-users/SKILL-MOBILE.md` → Flutter section |
| Page Properties | `fullstory-page-properties/SKILL-MOBILE.md` → Flutter section |
| Analytics Events | `fullstory-analytics-events/SKILL-MOBILE.md` → Flutter section |
| Stable Selectors | `fullstory-stable-selectors/SKILL-MOBILE.md` → Flutter section |
| Logging | `fullstory-logging/SKILL-MOBILE.md` → Flutter section |

### React Native

| Skill | Implementation File |
|-------|---------------------|
| Privacy Controls | `fullstory-privacy-controls/SKILL-MOBILE.md` → React Native section |
| Identify Users | `fullstory-identify-users/SKILL-MOBILE.md` → React Native section |
| Page Properties | `fullstory-page-properties/SKILL-MOBILE.md` → React Native section |
| Analytics Events | `fullstory-analytics-events/SKILL-MOBILE.md` → React Native section |
| Stable Selectors | `fullstory-stable-selectors/SKILL-MOBILE.md` → React Native section |
| Logging | `fullstory-logging/SKILL-MOBILE.md` → React Native section |

---

## ✅ GOOD Implementation: Complete Flow

This example demonstrates the correct sequence for a checkout flow:

```
USER JOURNEY: Browse → Login → View Product → Add to Cart → Checkout

┌──────────────────────────────────────────────────────────────────────────┐
│ STAGE 1: APP LAUNCH                                                       │
│ ✅ Privacy controls applied to sensitive views                           │
│ ✅ Consent checked (if required)                                         │
│ ✅ SDK initialized                                                       │
└──────────────────────────────────────────────────────────────────────────┘
                                    │
                                    v
┌──────────────────────────────────────────────────────────────────────────┐
│ USER BROWSES (Anonymous)                                                  │
│ ✅ Page properties set for each screen visited                           │
│ ✅ Events tracked (product_viewed, search_performed)                     │
│ ✅ Stable selectors on interactive elements                              │
└──────────────────────────────────────────────────────────────────────────┘
                                    │
                                    v
┌──────────────────────────────────────────────────────────────────────────┐
│ STAGE 2: USER LOGS IN                                                     │
│ ✅ identify(uid: user.id) called on login success                        │
│ ✅ User properties set (plan, role)                                      │
│ ✅ Previous anonymous session merged with identified user                │
└──────────────────────────────────────────────────────────────────────────┘
                                    │
                                    v
┌──────────────────────────────────────────────────────────────────────────┐
│ STAGE 3: PRODUCT DETAIL SCREEN                                            │
│ ✅ pageName = "Product Detail"                                           │
│ ✅ Page properties: productId, productName, price, category              │
│ ✅ Stable selectors: ProductDetail.add-to-cart, ProductDetail.wishlist   │
└──────────────────────────────────────────────────────────────────────────┘
                                    │
                                    v
┌──────────────────────────────────────────────────────────────────────────┐
│ STAGE 4: USER TAPS "ADD TO CART"                                          │
│ ✅ trackEvent("Product Added to Cart", { quantity: 1 })                  │
│ ✅ Event automatically inherits page context (productId, etc.)           │
└──────────────────────────────────────────────────────────────────────────┘
                                    │
                                    v
┌──────────────────────────────────────────────────────────────────────────┐
│ STAGE 3: CHECKOUT SCREEN                                                  │
│ ✅ pageName = "Checkout"                                                 │
│ ✅ Page properties: cartValue, itemCount                                 │
│ ✅ Privacy: Payment fields excluded (fs-exclude)                         │
│ ✅ Stable selectors: Checkout.submit-button                              │
└──────────────────────────────────────────────────────────────────────────┘
                                    │
                                    v
┌──────────────────────────────────────────────────────────────────────────┐
│ STAGE 4: PURCHASE COMPLETED                                               │
│ ✅ trackEvent("Purchase Completed", { orderId, revenue })                │
│ ✅ Error handling: If API fails, log error category (not details)        │
└──────────────────────────────────────────────────────────────────────────┘
```

---

## ❌ BAD Implementation: Out-of-Order Execution

```
┌──────────────────────────────────────────────────────────────────────────┐
│ ❌ BAD: Tracking event without page context                               │
│                                                                          │
│ // User tapped button, but no page.start() was called                    │
│ FS.track("Button Tapped", { label: "Submit" })                          │
│                                                                          │
│ Problem: Event is "homeless" - no page context, hard to analyze          │
└──────────────────────────────────────────────────────────────────────────┘

┌──────────────────────────────────────────────────────────────────────────┐
│ ❌ BAD: Identifying after events                                          │
│                                                                          │
│ FS.track("Product Viewed", { productId: "123" })  // Anonymous           │
│ FS.track("Added to Cart", { productId: "123" })   // Still anonymous     │
│ // ... later ...                                                         │
│ FS.identify(userId)  // NOW identified, but events already sent          │
│                                                                          │
│ Problem: Early events won't be linked to user                            │
└──────────────────────────────────────────────────────────────────────────┘

┌──────────────────────────────────────────────────────────────────────────┐
│ ❌ BAD: Missing privacy controls                                          │
│                                                                          │
│ // Screen shown with credit card input visible to Fullstory              │
│ FS.setPage("Checkout")                                                   │
│ // fs-exclude NOT applied to payment fields!                             │
│                                                                          │
│ Problem: Sensitive data may be captured                                   │
└──────────────────────────────────────────────────────────────────────────┘
```

---

## KEY TAKEAWAYS FOR AGENT

### Orchestration Rules

1. **Privacy First**: Never track anything until privacy masking is applied to sensitive views

2. **Identity Early**: Call `identify` as soon as auth state is confirmed to avoid anonymous session segments

3. **Page Before Events**: Every `trackEvent` should occur within an established page context

4. **Error Safety**: Pair every user action tracking with error handling in the catch block

5. **Stable Selectors Always**: Every interactive element should have a stable identifier

### Questions to Ask Developers

1. "What platform are you building for?" → Route to correct SKILL-MOBILE.md section
2. "At what point do you know the user's identity?" → Identify timing
3. "What screens contain sensitive information?" → Privacy controls
4. "What are the key user actions you want to track?" → Event planning
5. "How do you handle errors in your app?" → Error logging strategy

### Routing Logic

```
Developer asks about mobile Fullstory instrumentation
│
├─ "When/how to initialize?" → Stage 1 + privacy-controls/SKILL-MOBILE.md
├─ "When to identify users?" → Stage 2 + identify-users/SKILL-MOBILE.md
├─ "How to track screens?" → Stage 3 + page-properties/SKILL-MOBILE.md
├─ "How to track events?" → Stage 4 + analytics-events/SKILL-MOBILE.md
├─ "How to make elements searchable?" → stable-selectors/SKILL-MOBILE.md
└─ "How to handle errors?" → logging/SKILL-MOBILE.md
```

---

## REFERENCE LINKS

### Core SKILL-MOBILE.md Files
- **Privacy Controls**: `../core/fullstory-privacy-controls/SKILL-MOBILE.md`
- **Identify Users**: `../core/fullstory-identify-users/SKILL-MOBILE.md`
- **Page Properties**: `../core/fullstory-page-properties/SKILL-MOBILE.md`
- **Analytics Events**: `../core/fullstory-analytics-events/SKILL-MOBILE.md`
- **Logging**: `../core/fullstory-logging/SKILL-MOBILE.md`
- **Stable Selectors**: `../framework/fullstory-stable-selectors/SKILL-MOBILE.md`

### Fullstory Mobile SDK Documentation
- **iOS**: https://developer.fullstory.com/mobile/ios/
- **Android**: https://developer.fullstory.com/mobile/android/
- **React Native**: https://developer.fullstory.com/mobile/react-native/
- **Flutter**: https://developer.fullstory.com/mobile/flutter/

---

*This orchestrator skill provides sequencing logic for mobile instrumentation. For implementation details, see the referenced SKILL-MOBILE.md files.*

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/fullstorydev) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
