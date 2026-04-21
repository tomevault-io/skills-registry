---
name: experience-engineer
description: >- Use when this capability is needed.
metadata:
  author: derianandre
---

# Frontend Architect (Experience Engineer)

## Role

You are a **Frontend Craftsperson**. Every interaction should feel **Delightful** (art), **Intuitive** (design), and **Instant** (engineering).

---

## Quick Reference

### Core Web Vitals Targets

| Metric  | Target  | Tool       |
| ------- | ------- | ---------- |
| **LCP** | < 2.5s  | Lighthouse |
| **FID** | < 100ms | Lighthouse |
| **CLS** | < 0.1   | Lighthouse |
| **FCP** | < 1.8s  | Lighthouse |

### State Management Strategy

| Complexity  | Tool               | Choice                    |
| ----------- | ------------------ | ------------------------- |
| **Simple**  | useState + Context | Local component state     |
| **Medium**  | Zustand            | Global client state       |
| **Complex** | XState             | Workflows, state machines |
| **Server**  | TanStack Query     | Caching, server state     |

---

## When to Use This Skill

Activate `experience-engineer` when:

- 🎯 Designing state management strategy
- ⚡ Optimizing performance (bundle, runtime)
- 🔄 Implementing real-time features (WebSockets, SSE)
- 📦 Configuring build tools (Vite, Webpack)
- 🌐 Using advanced Web APIs

---

<!-- resources -->

## Implementation Patterns

### 1. State Management (Zustand)

```typescript
export const useCart = create<CartStore>((set) => ({
  items: [],
  addItem: (item) => set((s) => ({ items: [...s.items, item] })),
}));
```

### 2. Performance Optimization

- **Lazy Loading:** `const Heavy = lazy(() => import('./Heavy'))`.
- **Image Optimization:** Next.js `Image` component with priority for fold.
- **Virtualization:** `@tanstack/react-virtual` for 1000+ items.

### 3. Animation (Framer Motion)

- Use **FLIP** technique.
- GPU-Accelerated: `transform`, `opacity`.

### 4. Build Strategy (Vite)

- **Manual Chunks:** Split vendor (react) from UI (radix).
- **Minification:** Terser with `drop_console: true`.

---

## Type Safety (E2E)

- **tRPC:** Share types between server and client without code generation.
- **Zod:** Runtime validation for API responses.

---

## References

- [Core Web Vitals Guide](https://web.dev/vitals/)
- [XState Documentation](https://xstate.js.org/)
- [TanStack Query](https://tanstack.com/query)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/derianandre) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
