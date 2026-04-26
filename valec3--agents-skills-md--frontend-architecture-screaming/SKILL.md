---
name: frontend-architecture-screaming
description: Standardized guidelines for Screaming Architecture where folder and file names immediately reveal the business domain. Use when you want the codebase to communicate its purpose at a glance. Use when this capability is needed.
metadata:
  author: valec3
---

# Frontend Architecture Screaming

## When to use this skill
- Business logic should be obvious from folder structure
- Onboarding new developers quickly
- Domain-first (not tech-first) organization
- Clear separation between business and technical concerns

## Workflow
- [ ] Organize by **what** the app does (not how)
- [ ] Name folders after business concepts
- [ ] Technical details (React, API) are secondary
- [ ] Make architecture decisions visible

## Instructions

### Screaming vs Generic Structure

**❌ Generic (Tech-First)**
```
src/
├── components/
├── hooks/
├── services/
└── utils/
```
*Problem: Doesn't tell you what the app does*

**✅ Screaming (Domain-First)**
```
src/
├── OrderManagement/
│   ├── OrderApproval/
│   ├── OrderTracking/
│   └── OrderHistory/
├── InventoryControl/
│   ├── StockManagement/
│   └── SupplierOrders/
├── CustomerSupport/
│   ├── TicketSystem/
│   └── LiveChat/
└── _infrastructure/    # Technical plumbing
```

### Key Principles

**1. Business Concepts First**
```
features/
├── ProductCatalog/
│   ├── ProductSearch/
│   │   ├── SearchBar.tsx
│   │   ├── SearchResults.tsx
│   │   └── useProductSearch.ts
│   ├── ProductDetails/
│   └── ProductReviews/
```

**2. Technical Folders are Hidden**
Prefix with `_` to de-emphasize:
```
_infrastructure/
├── _api/
├── _routing/
└── _state/
```

**3. Naming Convention**
- Use **business terms** from domain experts
- Avoid generic names like "Manager", "Handler"
- Be explicit: `OrderApprovalWorkflow` not `Workflow`

**4. Example Structure**
```
src/
├── Appointment Scheduling/
│   ├── BookAppointment/
│   ├── CancelAppointment/
│   └── RescheduleAppointment/
├── Patient Records/
│   ├── MedicalHistory/
│   ├── TestResults/
│   └── Prescriptions/
├── Billing/
│   ├── GenerateInvoice/
│   ├── ProcessPayment/
│   └── InsuranceClaims/
└── _infrastructure/
    ├── _api/
    ├── _components/  # Generic UI
    └── _utils/
```

## Resources
- Business value is immediately visible
- Technical implementation is secondary
- Folder structure = table of contents for business

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/valec3) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
