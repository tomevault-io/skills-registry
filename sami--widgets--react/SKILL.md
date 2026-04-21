---
name: react
description: Use when working with the library for web and native user interfaces
metadata:
  author: sami
---

# React Skill

## Best Practices
1.  **Functional Components**: Always use functional components with Hooks. Class components are legacy.
2.  **Hooks Rules**: Only call hooks at the top level. Don't call them inside loops/conditions.
3.  **Encapsulation**: Keep components small. If it does 2 things, split it.
4.  **State Management**:
    *   `useState` for local UI state.
    *   `useContext` for low-frequency global state (User, Theme).
    *   `Zustand/Redux` for high-frequency complex state.

## Common Pitfalls
*   **Prop Drilling**: Passing props down 5 levels. Use Composition (`children`) or Context.
*   **Effect Dependencies**: Ignoring lint warnings for `useEffect` deps array. This causes stale closures or infinite loops.
*   **Key Prop**: Using `index` as key for dynamic lists. Use unique IDs.

## References
*   [React Docs](https://react.dev)
*   [Antigravity Component Template](../templates/react-component-template.jsx)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sami) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
