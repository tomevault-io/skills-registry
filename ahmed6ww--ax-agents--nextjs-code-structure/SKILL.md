---
name: nextjs-structure
description: Architect scalable Next.js applications using Feature-Sliced Design and Server Component patterns. Use when this capability is needed.
metadata:
  author: ahmed6ww
---

# Next.js Enterprise Standards

## 1. Feature-Sliced Directory Structure
Avoid a monolithic `components/` folder. Structure the frontend to mirror the backend domain boundaries.
**Adopt Structure by Feature [26]:**

```text
src/
  features/
    auth/
      components/    # UI specifically for Auth
      hooks/         # Auth logic
      actions.ts     # Server Actions for Auth
    billing/
      components/
      types/
  ui/                # Shared, domain-agnostic UI (Buttons, Inputs)
  app/               # Only for Routing and Layouts (Thin layer) 
Why: Code refactoring becomes easier because feature-specific logic is colocated. You can delete the features/billing folder and know you removed 100% of the billing code.
2. Server vs. Client Component Boundaries
• Default to Server: All components are Server Components by default. They can access the DB directly (acting as the Presentation Layer).
• Client Boundaries: Push use client as far down the tree as possible (leaves).
• Data Fetching: Do NOT fetch data in useEffect (client). Fetch data in Server Components and pass it down as props. This acts as the "Controller" in MVC terms, preparing data for the View.
3. The Anti-Corruption Layer (API Integration)
Do not call fetch('/api/users') directly inside UI components.
• Pattern: Use a Gateway/Service Layer on the frontend.
• Implementation: Create a typed SDK or generic HTTP wrapper (e.g., api.users.get()).
• Why: This acts as a "Gateway" to external resources. If the backend API schema changes, you update the Gateway, not 500 UI components.
4. State Management
• URL as State: For 100M+ users, rely on URL parameters for filter/sort state (Client Session State). This allows shareable links and reduces complex Redux/Context overhead.
• Server State: Use React Query or SWR for caching server data on the client. Treat it as a sync mechanism, not local state management.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ahmed6ww) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
