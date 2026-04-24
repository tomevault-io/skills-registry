---
name: analytics-tracking
description: > Use when this capability is needed.
metadata:
  author: carlosverasteguii
---

# Analytics Tracking & Measurement Strategy

## Core Principles

1.  **Track for Decisions, Not Curiosity:** Every event should help answer a question (e.g., "Do users abandon the receipt upload form?").
2.  **Naming Conventions (Segment/Rudderstack style):**
    *   `Object Action` (e.g., `Receipt Uploaded`, `Budget Created`).
    *   Properties: `snake_case` (e.g., `total_amount`, `merchant_name`).
3.  **Data Quality Beats Volume:** It's better to track 5 key events perfectly than 100 broken ones.

## Recommended Tracking Plan for Daticket

### Key Events
| Event Name | Trigger | Key Properties |
| :--- | :--- | :--- |
| `User Signed Up` | Successful registration | `method` (email/google), `role` |
| `Receipt Scanned` | User completes scan | `store_name`, `total_amount`, `item_count`, `ocr_confidence` |
| `Receipt Verified` | User confirms/edits OCR | `edits_made` (boolean) |
| `Budget Created` | New budget set | `category`, `amount`, `period` |
| `Alert Triggered` | Spending > Budget | `category`, `percentage_over` |

### Measurement Readiness Checklist

- [ ] **Event Definition:** Are all events clearly defined in a Tracking Plan?
- [ ] **Identity Management:** Are we correctly identifying users across sessions?
- [ ] **Validation:** Have we verified that the tracked data matches the DB data?
- [ ] **Privacy:** Are we PII-compliant? (Avoid tracking passwords, full data in URLs).

## Implementation Tips
- Use a wrapper function for tracking (e.g., `trackEvent(name, props)`) to allow swapping providers (Supabase Analytics, PostHog, GA4) easily.
- Log tracking events to console in Development mode for debugging.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/carlosverasteguii) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
