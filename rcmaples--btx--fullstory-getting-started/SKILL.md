---
name: fullstory-getting-started
description: Comprehensive getting started guide for Fullstory Browser API v2 implementation. Provides the complete initialization flow, API overview, decision framework for which APIs to use, industry-specific guidance, privacy best practices, and framework integration patterns. Links to all core, meta, industry, and framework skills. Use when this capability is needed.
metadata:
  author: rcmaples
---

# Fullstory Browser API v2: Getting Started Guide

> **📋 Version Note**: This guide covers Fullstory Browser API **v2**. Always verify API syntax against the [official documentation](https://developer.fullstory.com/browser/getting-started/) as the API may evolve. Rate limits, parameters, and features are subject to change.

## Overview

This guide provides everything you need to implement Fullstory's Browser API v2 in your web application. It covers:

1. **Initial Setup**: Snippet installation and configuration
2. **API Landscape**: Understanding all available APIs
3. **Implementation Order**: What to implement first
4. **Decision Framework**: Choosing the right API for your data
5. **Privacy Considerations**: What to capture, mask, or exclude
6. **Industry-Specific Guidance**: Best practices for your vertical
7. **Skill Navigation**: Finding the right detailed guide

---

## Complete Skill Inventory

### Core API Skills

| Skill                          | Purpose                     | Use When                  |
| ------------------------------ | --------------------------- | ------------------------- |
| `fullstory-identify-users`     | Link sessions to users      | Implementing login flow   |
| `fullstory-anonymize-users`    | End identified sessions     | Implementing logout flow  |
| `fullstory-user-properties`    | Set user attributes         | Adding segmentation data  |
| `fullstory-page-properties`    | Set page context            | SPA navigation, page data |
| `fullstory-element-properties` | Capture interaction context | Product grids, lists      |
| `fullstory-analytics-events`   | Track discrete actions      | Conversions, features     |
| `fullstory-observe-callbacks`  | React to FS lifecycle       | Session URL for support   |
| `fullstory-logging`            | Add logs to session         | Error debugging           |
| `fullstory-user-consent`       | Control capture consent     | GDPR/CCPA compliance      |
| `fullstory-capture-control`    | Pause/resume capture        | Sensitive screens         |
| `fullstory-async-methods`      | Promise-based API calls     | Modern async flows        |
| `fullstory-privacy-controls`   | Mask/exclude elements       | PII protection            |

### Strategy & Meta Skills

| Skill                                   | Purpose                | Use When                |
| --------------------------------------- | ---------------------- | ----------------------- |
| `fullstory-privacy-strategy`            | Data privacy decisions | Planning implementation |
| `universal-data-scoping-and-decoration` | Where to put data      | Deciding scope          |

### Industry-Specific Skills

| Skill                           | Industry             | Key Considerations               |
| ------------------------------- | -------------------- | -------------------------------- |
| `fullstory-banking`             | Banking & Financial  | PCI, GLBA, transaction masking   |
| `fullstory-ecommerce`           | E-commerce & Retail  | Conversion funnels, product data |
| `fullstory-gaming`              | Gaming               | Responsible gaming, KYC          |
| `fullstory-healthcare`          | Healthcare           | HIPAA, PHI exclusion             |
| `fullstory-saas`                | B2B SaaS             | Feature adoption, churn          |
| `fullstory-travel`              | Travel & Hospitality | Booking funnels, PCI             |
| `fullstory-media-entertainment` | Media & Streaming    | Engagement, subscriptions        |

---

## Industry Comparison at a Glance

Different industries have vastly different requirements for Fullstory implementation. This table summarizes the key differences:

### Privacy Defaults by Industry

| Industry       | Default Privacy Mode | Financial Data        | User Content   | Conversion Tracking | Primary Concern         |
| -------------- | -------------------- | --------------------- | -------------- | ------------------- | ----------------------- |
| **Banking**    | Exclude              | Exclude (ranges only) | Exclude        | Limited             | Regulatory (PCI, GLBA)  |
| **E-commerce** | Unmask               | Capture (orders)      | Mostly capture | Rich                | Conversion optimization |
| **Gaming**     | Mixed                | Exclude (ranges only) | Exclude        | Careful             | Responsible gaming      |
| **Healthcare** | Exclude              | Exclude               | Exclude        | Very limited        | HIPAA compliance        |
| **SaaS**       | Unmask               | Usually OK            | Mask/Consider  | Rich                | Feature adoption        |
| **Travel**     | Unmask               | Capture (bookings)    | Mask           | Rich                | Booking optimization    |
| **Media**      | Unmask               | N/A                   | Capture        | Rich                | Engagement metrics      |

### What to Capture by Industry

| Data Type             | Banking   | E-commerce    | Gaming          | Healthcare | SaaS        | Travel     | Media         |
| --------------------- | --------- | ------------- | --------------- | ---------- | ----------- | ---------- | ------------- |
| User names            | ❌        | ⚠️ Mask       | ⚠️ Mask         | ❌         | ⚠️ Mask     | ⚠️ Mask    | ⚠️ Mask       |
| Email                 | ❌        | ⚠️ Hash       | ⚠️ Hash         | ❌         | ⚠️ Consider | ⚠️ Mask    | ⚠️ Consider   |
| Product/content names | N/A       | ✅            | ✅ Events/games | N/A        | ✅ Features | ✅         | ✅            |
| Prices/amounts        | ❌ Ranges | ✅            | ❌ Ranges       | ❌         | ✅          | ✅         | ✅ Sub prices |
| Account balance       | ❌        | N/A           | ❌              | N/A        | N/A         | N/A        | N/A           |
| Transaction details   | ❌        | ✅ Order data | ❌              | ❌         | ✅ Usage    | ✅ Booking | ✅ Viewing    |
| Search queries        | ⚠️        | ✅            | ⚠️              | ❌         | ✅          | ✅         | ✅            |
| Payment cards         | ❌        | ❌            | ❌              | ❌         | ❌          | ❌         | ❌            |
| Government IDs        | ❌        | ❌            | ❌              | ❌         | ❌          | ❌         | ❌            |

**Legend**: ✅ = Capture, ⚠️ = Consider/Mask, ❌ = Exclude

### Key Regulatory Requirements

| Industry       | Primary Regulations      | Fullstory Requirements                               |
| -------------- | ------------------------ | ---------------------------------------------------- |
| **Banking**    | PCI DSS, GLBA, SOX       | BAA not typically needed; exclude all financial data |
| **E-commerce** | PCI DSS, CCPA, GDPR      | Exclude payment fields; consent for EU               |
| **Gaming**     | Gaming licenses, AML/KYC | Never analyze gaming patterns; exclude amounts       |
| **Healthcare** | HIPAA, HITECH            | BAA required; exclude ALL PHI; masking insufficient  |
| **SaaS**       | SOC 2, GDPR              | Enterprise privacy options; consent for EU           |
| **Travel**     | PCI DSS, GDPR            | Exclude passport/ID numbers; payment exclusion       |
| **Media**      | COPPA, GDPR              | Never track children's profiles; consent             |

---

## I. The Fullstory API Ecosystem

### Complete API Reference

| API                    | Method                                       | Purpose                     | Skill Document                 |
| ---------------------- | -------------------------------------------- | --------------------------- | ------------------------------ |
| **Identity**           | `FS('setIdentity', { uid, properties })`     | Identify known users        | `fullstory-identify-users`     |
| **Anonymize**          | `FS('setIdentity', { anonymous: true })`     | End identified session      | `fullstory-anonymize-users`    |
| **User Properties**    | `FS('setProperties', { type: 'user', ... })` | Update user attributes      | `fullstory-user-properties`    |
| **Page Properties**    | `FS('setProperties', { type: 'page', ... })` | Set page context            | `fullstory-page-properties`    |
| **Element Properties** | `data-fs-*` attributes                       | Capture interaction context | `fullstory-element-properties` |
| **Events**             | `FS('trackEvent', { name, properties })`     | Track discrete actions      | `fullstory-analytics-events`   |
| **Observers**          | `FS('observe', { type, callback })`          | React to FS lifecycle       | `fullstory-observe-callbacks`  |
| **Logging**            | `FS('log', { level, msg })`                  | Add logs to session         | `fullstory-logging`            |
| **Consent**            | `FS('setIdentity', { consent: bool })`       | Control capture consent     | `fullstory-user-consent`       |
| **Capture Control**    | `FS('shutdown')` / `FS('restart')`           | Pause/resume capture        | `fullstory-capture-control`    |
| **Async Methods**      | `FS('*Async')` variants                      | Promise-based API calls     | `fullstory-async-methods`      |

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

## II. Snippet Installation

### Basic Snippet (v2)

Add this snippet to your HTML `<head>`:

```html
<script>
  window['_fs_host'] = 'fullstory.com'
  window['_fs_script'] = 'edge.fullstory.com/s/fs.js'
  window['_fs_org'] = 'YOUR_ORG_ID'
  window['_fs_namespace'] = 'FS'

  ;(function (m, n, e, t, l, o, g, y) {
    if (e in m) {
      if (m.console && m.console.log) {
        m.console.log('Fullstory namespace conflict. Please set window["_fs_namespace"].')
      }
      return
    }
    g = m[e] = function (a, b, s) {
      g.q ? g.q.push([a, b, s]) : g._api(a, b, s)
    }
    g.q = []
    o = n.createElement(t)
    o.async = 1
    o.crossOrigin = 'anonymous'
    o.src = 'https://' + _fs_script
    y = n.getElementsByTagName(t)[0]
    y.parentNode.insertBefore(o, y)
    g.identify = function (i, v, s) {
      g(l, {uid: i}, s)
      if (v) g(l, v, s)
    }
    g.setUserVars = function (v, s) {
      g(l, v, s)
    }
    g.event = function (i, v, s) {
      g('event', {n: i, p: v}, s)
    }
    g.anonymize = function () {
      g.identify(!!0)
    }
    g.shutdown = function () {
      g('rec', !1)
    }
    g.restart = function () {
      g('rec', !0)
    }
    g.log = function (a, b) {
      g('log', [a, b])
    }
    g.consent = function (a) {
      g('consent', !arguments.length || a)
    }
    g.identifyAccount = function (i, v) {
      o = 'account'
      v = v || {}
      v.acctId = i
      g(o, v)
    }
    g.clearUserCookie = function () {}
    g.setVars = function (n, p) {
      g('setVars', [n, p])
    }
    g._w = {}
    y = 'XMLHttpRequest'
    g._w[y] = m[y]
    y = 'fetch'
    g._w[y] = m[y]
    if (m[y])
      m[y] = function () {
        return g._w[y].apply(this, arguments)
      }
    g._v = '2.0.0'
  })(window, document, window['_fs_namespace'], 'script', 'identify')
</script>
```

### Configuration Options

| Option                   | Description                    | Default                        |
| ------------------------ | ------------------------------ | ------------------------------ |
| `_fs_org`                | Your Fullstory organization ID | **Required**                   |
| `_fs_namespace`          | Global variable name           | `'FS'`                         |
| `_fs_capture_on_startup` | Start capturing immediately    | `true`                         |
| `_fs_host`               | Fullstory host                 | `'fullstory.com'`              |
| `_fs_script`             | Script location                | `'edge.fullstory.com/s/fs.js'` |

### How Fullstory Tracks Users (First-Party Cookies)

Fullstory uses **first-party cookies** set on YOUR domain to track users:

| Cookie   | Duration | Purpose                                            |
| -------- | -------- | -------------------------------------------------- |
| `fs_uid` | 1 year   | Tracks user across sessions (the "capture cookie") |
| `fs_cid` | 1 year   | Stores consent state for the device                |
| `fs_lua` | 30 min   | Last user action timestamp (session lifecycle)     |

**Why First-Party Cookies Matter:**

| Aspect              | First-Party (Fullstory)                  | Third-Party Cookies     |
| ------------------- | ---------------------------------------- | ----------------------- |
| Domain              | Set on YOUR domain                       | Set on external domain  |
| Browser blocking    | ✅ Not blocked by browsers/ad-blockers   | ❌ Often blocked        |
| Cross-site tracking | ❌ Cannot track across different sites   | ✅ Can track across web |
| Privacy             | ✅ Your data stays isolated to your site | ❌ Data aggregated      |

**Key Behaviors:**

- **Anonymous users persist**: Same `fs_uid` cookie = same user across sessions (until cookie expires/deleted)
- **Session merging**: When `setIdentity` is called, ALL previous anonymous sessions from that cookie merge into the identified user
- **Anonymization resets**: `setIdentity({ anonymous: true })` generates a NEW `fs_uid` cookie, breaking the link to previous sessions

> **Reference**: [Why Fullstory uses First-Party Cookies](https://help.fullstory.com/hc/en-us/articles/360020829513-Why-Fullstory-uses-First-Party-Cookies)

### Private by Default Mode (Recommended for Sensitive Industries)

Fullstory offers a **Private by Default** capture mode that inverts the default behavior:

| Mode                   | Default Behavior                        | Your Action                                                |
| ---------------------- | --------------------------------------- | ---------------------------------------------------------- |
| **Standard**           | Everything captured (unmask is default) | Add `fs-mask` / `fs-exclude` to protect sensitive elements |
| **Private by Default** | Everything masked                       | Add `fs-unmask` to reveal safe content                     |

**When to Use Private by Default:**

- ✅ Banking / Financial services
- ✅ Healthcare / HIPAA-regulated
- ✅ Enterprise SaaS with customer data
- ✅ Any app where "default open" is too risky

**Enable via:**

- **New accounts**: Select during onboarding wizard
- **Existing accounts**: Contact [Fullstory Support](https://help.fullstory.com/hc/en-us/requests/new)

> **Reference**: [Fullstory Private by Default](https://help.fullstory.com/hc/en-us/articles/360044349073-Fullstory-Private-by-Default)

### Consent-Required Configuration (GDPR)

For GDPR compliance, don't capture until consent is given:

```html
<script>
  window['_fs_capture_on_startup'] = false // Don't capture until consent
  window['_fs_org'] = 'YOUR_ORG_ID'
  // ... rest of snippet
</script>
```

Then call when consent is granted:

```javascript
FS('setIdentity', {consent: true})
```

---

## III. Implementation Order

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
```

### Minimum Viable Implementation

For a basic integration, implement these APIs:

```javascript
// 1. Identify users on login
FS('setIdentity', {
  uid: user.id,
  properties: {
    displayName: user.name,
    email: user.email,
  },
})

// 2. Set page context
FS('setProperties', {
  type: 'page',
  properties: {
    pageName: 'Dashboard',
  },
})

// 3. Track key events
FS('trackEvent', {
  name: 'Feature Used',
  properties: {
    featureName: 'export',
  },
})

// 4. Anonymize on logout
FS('setIdentity', {anonymous: true})
```

---

## IV. Privacy First: Three Privacy Modes

Before capturing any data, understand the three privacy modes:

### Privacy Mode Comparison

| Mode        | CSS Class     | What Leaves Device | Events Captured | Use For                           |
| ----------- | ------------- | ------------------ | --------------- | --------------------------------- |
| **Exclude** | `.fs-exclude` | ❌ Nothing         | ❌ No           | Passwords, SSN, PHI, card numbers |
| **Mask**    | `.fs-mask`    | ⚠️ Structure only  | ✅ Yes          | Names, emails, addresses          |
| **Unmask**  | `.fs-unmask`  | ✅ Everything      | ✅ Yes          | Public content, products          |

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

| Industry   | Default Recommendation                                |
| ---------- | ----------------------------------------------------- |
| Healthcare | Use Private by Default mode; explicit unmask only     |
| Banking    | Exclude financial data; mask PII                      |
| Gaming     | Exclude amounts; mask user info                       |
| E-commerce | Unmask products; mask checkout PII; exclude payment   |
| SaaS       | Unmask features; mask user content                    |
| Travel     | Unmask search/booking; mask traveler PII; exclude IDs |
| Media      | Unmask content; mask profiles                         |

---

## V. Quick Decision Guide

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

### Quick Reference Table

| I want to...            | Use this API              | Skill                          |
| ----------------------- | ------------------------- | ------------------------------ |
| Link sessions to a user | `setIdentity`             | `fullstory-identify-users`     |
| Add user attributes     | `setProperties(user)`     | `fullstory-user-properties`    |
| Set page context        | `setProperties(page)`     | `fullstory-page-properties`    |
| Track what was clicked  | Element Properties        | `fullstory-element-properties` |
| Track a conversion      | `trackEvent`              | `fullstory-analytics-events`   |
| Handle logout           | `setIdentity(anonymous)`  | `fullstory-anonymize-users`    |
| Get session URL         | `observe` or `getSession` | `fullstory-observe-callbacks`  |
| Implement GDPR consent  | `setIdentity(consent)`    | `fullstory-user-consent`       |
| Pause recording         | `shutdown`/`restart`      | `fullstory-capture-control`    |
| Log errors to session   | `log`                     | `fullstory-logging`            |
| Protect sensitive data  | CSS classes               | `fullstory-privacy-controls`   |

---

## VI. Industry-Specific Quick Start

### Banking/Financial Services

```javascript
// Use internal customer ID, never SSN or account numbers
FS('setIdentity', {uid: customer.customerId})

// Exclude all financial data, use ranges if needed
// Add fs-exclude to: balances, transactions, account numbers
FS('trackEvent', {
  name: 'transfer_completed',
  properties: {
    transfer_type: 'internal',
    amount_range: '$100-$500', // Never exact amount
  },
})
```

**→ See `fullstory-banking` for complete guide**

### E-commerce

```javascript
// Rich product tracking is valuable
FS('setProperties', {
  type: 'page',
  properties: {
    pageName: 'Product Detail',
    productId: product.id,
    productName: product.name,
    price: product.price, // Prices are fine in e-commerce
  },
})

// Track conversions with full details
FS('trackEvent', {
  name: 'purchase_completed',
  properties: {
    order_id: order.id,
    revenue: order.total,
    item_count: order.items.length,
  },
})
```

**→ See `fullstory-ecommerce` for complete guide**

### Healthcare

```javascript
// Extremely limited capture - use anonymous sessions if possible
FS('setIdentity', {uid: generateSessionId()}) // No linking to patient

// Exclude EVERYTHING medical - masking is NOT sufficient
// Use Private by Default mode
// Only track: navigation, errors, page load times
```

**→ See `fullstory-healthcare` for complete guide**

### SaaS

```javascript
// Rich user identification for feature adoption
FS('setIdentity', {
  uid: user.id,
  properties: {
    displayName: user.firstName,
    plan: organization.plan,
    role: user.role,
    org_id: organization.id,
  },
})

// Track feature usage comprehensively
FS('trackEvent', {
  name: 'feature_first_use',
  properties: {
    feature_name: 'report_builder',
    days_since_signup: user.daysSinceSignup,
  },
})
```

**→ See `fullstory-saas` for complete guide**

---

## VII. Common Integration Patterns

### Pattern 1: React Integration

```jsx
// hooks/useFullstory.js
import {useEffect} from 'react'
import {useAuth} from './useAuth'
import {useLocation} from 'react-router-dom'

export function useFullstory() {
  const {user, isAuthenticated} = useAuth()
  const location = useLocation()

  // Handle user identification
  useEffect(() => {
    if (isAuthenticated && user) {
      FS('setIdentity', {
        uid: user.id,
        properties: {
          displayName: user.name,
          email: user.email,
          plan: user.plan,
        },
      })
    }
  }, [user, isAuthenticated])

  // Handle page changes
  useEffect(() => {
    const pageName = getPageName(location.pathname)
    FS('setProperties', {
      type: 'page',
      properties: {pageName},
    })
  }, [location])

  // Return helper functions
  return {
    trackEvent: (name, properties) => {
      FS('trackEvent', {name, properties})
    },
    setUserProperty: (properties) => {
      FS('setProperties', {type: 'user', properties})
    },
  }
}
```

### Pattern 2: Privacy-First Component Wrapper

```jsx
// components/PrivacyWrapper.js
export function SensitiveData({ children, level = 'mask' }) {
  const className = level === 'exclude' ? 'fs-exclude' : 'fs-mask';
  return <div className={className}>{children}</div>;
}

export function PublicData({ children }) {
  return <div className="fs-unmask">{children}</div>;
}

// Usage
<SensitiveData level="exclude">
  <CreditCardForm />
</SensitiveData>

<SensitiveData>
  <UserProfile />  {/* Masked by default */}
</SensitiveData>

<PublicData>
  <ProductGrid />  {/* Visible */}
</PublicData>
```

---

## VIII. Testing Your Integration

### 1. Verify Snippet Installation

Open browser console and check:

```javascript
typeof FS !== 'undefined' // Should be true
FS('getSession') // Should return session URL
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

## IX. Troubleshooting

### Fullstory Not Loading

```javascript
if (typeof FS === 'undefined') {
  console.error('Fullstory not loaded - check snippet installation')
}
```

**Common causes:**

- Snippet not in `<head>`
- Wrong org ID
- Ad blocker blocking script
- Content Security Policy blocking

### User Not Identified

```javascript
console.log('Identifying user:', user.id)
FS('setIdentity', {uid: user.id})
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

## X. Related Skills Reference

### Core Data Capture

| Skill                          | Use For                          |
| ------------------------------ | -------------------------------- |
| `fullstory-identify-users`     | User identification and login    |
| `fullstory-anonymize-users`    | Logout and user switching        |
| `fullstory-user-properties`    | User attributes and segmentation |
| `fullstory-page-properties`    | Page context and Journeys        |
| `fullstory-element-properties` | Interaction-level data           |
| `fullstory-analytics-events`   | Business events and funnels      |

### Privacy & Control

| Skill                        | Use For                       |
| ---------------------------- | ----------------------------- |
| `fullstory-privacy-controls` | Element masking and exclusion |
| `fullstory-privacy-strategy` | Data privacy decisions        |
| `fullstory-user-consent`     | GDPR/CCPA compliance          |
| `fullstory-capture-control`  | Pause/resume recording        |

### Lifecycle & Utilities

| Skill                         | Use For                          |
| ----------------------------- | -------------------------------- |
| `fullstory-async-methods`     | Promise-based API calls          |
| `fullstory-observe-callbacks` | Session URL and lifecycle events |
| `fullstory-logging`           | Error and debug logging          |

### Industry-Specific

| Skill                           | Use For                           |
| ------------------------------- | --------------------------------- |
| `fullstory-banking`             | Financial services implementation |
| `fullstory-ecommerce`           | E-commerce/retail implementation  |
| `fullstory-gaming`              | Gaming/gaming implementation      |
| `fullstory-healthcare`          | Healthcare/HIPAA implementation   |
| `fullstory-saas`                | B2B SaaS implementation           |
| `fullstory-travel`              | Travel/hospitality implementation |
| `fullstory-media-entertainment` | Media/streaming implementation    |

### Strategy

| Skill                                   | Use For                    |
| --------------------------------------- | -------------------------- |
| `universal-data-scoping-and-decoration` | Deciding where to put data |

### Framework Integration

| Skill                        | Use For                                                                  |
| ---------------------------- | ------------------------------------------------------------------------ |
| `fullstory-stable-selectors` | Stable `data-*` attributes for any framework (React, Angular, Vue, etc.) |

---

## Key Takeaways for Agent

When helping developers get started with Fullstory:

1. **Privacy first**:
   - Ask what industry/regulations apply
   - Point to appropriate industry skill
   - Default to more restrictive privacy in regulated industries

2. **Implementation order matters**:
   - Start with privacy controls
   - Then identification (login/logout)
   - Add page properties (pageName is crucial for Journeys)
   - Then events and element properties

3. **Minimum viable integration**:
   - Privacy classes on sensitive elements
   - setIdentity on login
   - setIdentity anonymous on logout
   - setProperties(page) with pageName
   - A few key trackEvent calls

4. **Industry matters**:
   - Healthcare: Default exclude, BAA required
   - Banking: Exclude financial data, use ranges
   - E-commerce: Can capture most product data
   - SaaS: Rich feature tracking is valuable

5. **Common first questions**:
   - "How do I link sessions to users?" → `fullstory-identify-users`
   - "Where should I put this data?" → `universal-data-scoping-and-decoration`
   - "How do I track conversions?" → `fullstory-analytics-events`
   - "What should I mask vs exclude?" → `fullstory-privacy-controls`

6. **Point to specific skills** for detailed implementation guidance

---

## Reference Links

- **Getting Started**: https://developer.fullstory.com/browser/getting-started/
- **Privacy Controls**: https://help.fullstory.com/hc/en-us/articles/360020623574
- **Custom Properties**: https://developer.fullstory.com/browser/custom-properties/
- **Help Center**: https://help.fullstory.com/

---

_This getting started guide provides the foundation for implementing Fullstory Browser API v2. For detailed implementation guidance on any specific API or industry, refer to the linked skill documents._

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rcmaples) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
