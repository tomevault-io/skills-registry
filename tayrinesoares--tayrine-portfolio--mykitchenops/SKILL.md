---
name: mykitchenops
description: This rule provide standard development practices for MyKitchenOps Use when this capability is needed.
metadata:
  author: tayrinesoares
---

# Overview

Insert overview text here. The agent will only see this should they choose to apply the rule.

Any new components or modifications complete them according to Screaming Architecture and SOLID principles.
PROJECT STRUCTURE:
• Modularized by features under /src/features/
• Each feature follows a STANDARDIZED structure:/feature/├── components/ (UI components - single responsibility)├── hooks/ (Custom hooks - data access & state management)├── services/ (Business logic - API calls, calculations)├── pages/ (Page/container components)├── store/ (Zustand store - feature state)├── data/ (Constants, column definitions)├── schemas/ (Zod validation schemas)└── types/ (TypeScript interfaces, if using TS)
STATE MANAGEMENT:
• Use Zustand for all state that persists or is shared
• Each store manages ONLY its feature's state
• Stores expose minimal, focused actions (Interface Segregation)
• Components consume state via custom hooks, NEVER calling services directly
SOLID PRINCIPLES TO FOLLOW:
1 Single Responsibility
◦ Components focus on UI rendering only
◦ Services contain business logic (API calls, calculations, transformations)
◦ Hooks mediate between UI and services
2 Open/Closed
◦ New features added without modifying existing code
◦ Features can be extended via hooks and stores
◦ Avoid modifying shared utilities unless necessary
3 Liskov Substitution
◦ Use consistent hook interfaces across features
◦ Hooks should have predictable, testable behavior
4 Interface Segregation
◦ Hooks return only what consumers need
◦ Stores expose minimal action sets
◦ Props are specific to component responsibility
5 Dependency Inversion
◦ Components depend on hooks/stores (abstractions)
◦ Components never call services directly
◦ Services don't import components
CODE ORGANIZATION REQUIREMENTS:
• Keep components small (<200 lines) and focused
• Extract reusable logic into services
• Use TypeScript for new features (gradual migration)
• Validate all inputs with Zod schemas
• Add JSDoc comments for complex functions
DATA FLOW (The "Screaming" part):
1 Component renders → needs data
2 Component calls hook → "I need ingredients for this org"
3 Hook checks store → is data cached?
4 If not cached, hook calls service → "Fetch ingredients from API"
5 Service queries Supabase → returns typed data
6 Hook updates store → cache the result
7 Hook returns data to component → component renders
ANTI-PATTERNS TO AVOID::x: Components calling services directly:x: Components managing global state locally:x: Services importing components:x: Tightly coupled feature folders:x: Logic scattered across multiple files:x: Inconsistent hook return types
MAKE THE CODE:
• Modular: Each piece has one job
• Maintainable: Clear separation of concerns
• Scalable: Add features without refactoring old ones
• Testable: Dependencies injected, no singletons
• Type-safe: Use TypeScript for critical paths
• Documented: JSDoc + clear naming. Do not change any other components and do not break the funcitonality per se.
Ensure:
1. you use the correct props and hooks everywhere you need to implement them
2. update the routes to reference the newly created files and the imports are all updated
3. Create and or update wiki documentation regarding any new functionality or development
4. Ensure any files or development is only accessbile depending on the permissions structure currently used

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tayrinesoares) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
