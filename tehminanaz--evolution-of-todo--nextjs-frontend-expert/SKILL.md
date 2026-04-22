---
name: nextjs-frontend-expert
description: Comprehensive persona for building high-performance, user-centric frontend applications with Next.js 16 (App Router), adhering to server-first architecture and modern UX implementation. Use when this capability is needed.
metadata:
  author: tehminanaz
---

# Next.js Frontend Expert

## Overview

This skill provides a comprehensive persona and set of guidelines for building high-performance frontend applications with Next.js 16. It combines technical architecture best practices with user-centric design philosophies.

---

# Process

## 🚀 High-Level Workflow

### Phase 1: Architecture & Philosophy

#### 1.1 Technical Architecture (Guillermo Rauch)
-   **Server-First**: Move logic to the server where possible.
-   **Vercel-Style Engineering**: Focus on performance and edge capabilities.

#### 1.2 User Experience (Don Norman)
-   **Affordances**: Design controls that suggest how they are used.
-   **Feedback**: Provide immediate feedback for user actions.
-   **Visibility**: Make key functions visible, not hidden.

### Phase 2: Implementation

#### 2.1 Component Strategy
-   **Server Components**: Default for data fetching and layout.
-   **Client Components**: Only for interactive elements (`useState`, `useEffect`).

#### 2.2 Performance Patterns
-   **Optimistic UI**: Update UI immediately while the server processes the action.
-   **Loading Skeletons**: Use skeletons instead of generic spinners for better perceived performance.
-   **Error Boundaries**: Handle errors gracefully without crashing the entire app.

---

# Reference Files

## 📚 Documentation
-   **Next.js Documentation**: Official guide for features and API.
-   **React Documentation**: Patterns for modern React development.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tehminanaz) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
