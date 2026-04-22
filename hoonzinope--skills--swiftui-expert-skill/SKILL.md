---
name: swiftui-expert-skill
description: SwiftUI development and iOS UX/HIG compliance. Use for building features, refactoring views, and HIG audits (targets, Dynamic Type, semantic colors, modern APIs, iOS 26+ Liquid Glass). Use when this capability is needed.
metadata:
  author: hoonzinope
---

# SwiftUI Expert Skill

## Overview
Build, review, or improve SwiftUI features with correct state management, modern API usage, and iOS 26+ Liquid Glass styling. Performs iOS UX audits aligned to Apple HIG.

## Workflow Decision Tree

### 1) Review existing SwiftUI code
- Check state management, modern APIs, and view structure (see `references/guidelines.md`)
- Verify performance and list patterns (see `references/performance-patterns.md`, `references/list-patterns.md`)
- Inspect animations and Liquid Glass (see `references/animation-basics.md`, `references/liquid-glass.md`)
- Run iOS UX/HIG audit (targets, colors, Dynamic Type, SF Symbols, navigation).

### 2) Improve existing SwiftUI code
- Audit property wrappers (prefer `@Observable`) and modern equivalents (see `references/guidelines.md`).
- Extract subviews and refactor hot paths (see `references/view-structure.md`, `references/performance-patterns.md`).
- Adopt Liquid Glass only when explicitly requested.

### 3) Implement new SwiftUI feature
- Design data flow first (see `references/state-management.md`).
- Use modern APIs and `@Observable` (see `references/guidelines.md`).
- Structure views for optimal diffing and apply iOS UX guardrails (44pt targets, semantic colors).

## Quick Reference
- `references/quick-reference.md` - Fast decision tables and high-frequency patterns.
- `references/review-checklist.md` - Consolidated audit checklist.
- `references/guidelines.md` - Detailed core guidelines for state, APIs, composition, and performance.

## Detailed References
- `references/state-management.md` - Data flow (prefer `@Observable`).
- `references/view-structure.md` - Composition and extraction.
- `references/performance-patterns.md` - Optimization and anti-patterns.
- `references/list-patterns.md` - ForEach and stable identity.
- `references/modern-apis.md` - API replacements and modern usage.
- `references/animation-basics.md` - Animation concepts and performance.
- `references/liquid-glass.md` - iOS 26+ Liquid Glass API.
- `references/external/platform-design-ios.md` - Apple HIG rules.

## Philosophy
Focus on facts and best practices: prioritize modern APIs, ensure thread safety with `@MainActor`, and follow Apple's Human Interface Guidelines.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hoonzinope) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
