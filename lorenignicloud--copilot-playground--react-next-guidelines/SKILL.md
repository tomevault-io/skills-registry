---
name: react-next-guidelines
description: Enforce React and Next.js standards for App Router, component structure, accessibility, and route loading parity. Use when implementing or refactoring React/Next.js UI and route files. Use when this capability is needed.
metadata:
  author: lorenignicloud
---

# React & Next.js Development Standards

## Priority order (apply in this order)

1. User request in the current task
2. Repository instructions and path-specific instructions
3. This prompt file
4. Context7 documentation for uncovered or ambiguous topics

When rules conflict, follow the highest-priority source.

## Context files

- UI Components: `components/ui`
- UI Components changes markdown: `components/ui/changes.md`
- Typography style reference skill: `css-typography-style-unification-guidelines`

Use these files before introducing new abstractions or style changes.

## Component structure & naming

### Component basics

- Component naming: use PascalCase; file name must match exported component name.
- Props destructuring: destructure props inside the component body, not in function parameters.
- Client component declaration: prefer `const ExampleComponent = (props: ExampleComponentProps) => { ... }`; avoid `React.FC` unless children typing is required.
- Server component declaration: use function declarations. In App Router route files, use default export when required by Next.js conventions.
- Props typing: define explicit props types (`type` or `interface`) and avoid implicit `any`.
- Readonly props: use `Readonly` utility type for props.

## Component creation & management

- Reuse first: check existing UI components before creating new ones.
- Props abstraction: prefer focused props over passing full domain objects.
- Core component scope: small/medium edits are allowed; major behavior or API changes must be explicitly agreed first.
- Change tracking: if modifying existing UI core components, add a short note to the UI components changes file.
- Pattern adherence: follow repository core component patterns when present.

## Component patterns

- Dual-purpose components: reuse components for view/edit flows when it keeps API clean.
- Complex state management: prefer `useReducer` for multi-action workflows in a single client component.
- Responsive UX: use `useTransition` for potentially expensive state updates (especially routing-driven UI updates).

## UX/UI standards

### Accessibility & design

- Accessibility: interactive elements must be keyboard accessible and labeled (`aria-*` where needed).
- Responsive design: components must behave correctly across common breakpoints.
- Simplicity first: prefer minimal UX that satisfies the requirement; avoid speculative complexity.

## Project libraries

### Core libraries

- Zustand: state management
- React Hook Form: form handling
- Zod: data validation
- Nuqs: URL state sync
- TanStack Query: data fetching/caching
- Next-Intl: internationalization
- next-themes: theme management

Do not introduce alternatives without a clear task-driven reason.

## Global directory structure & conventions

### Global directory mapping

- `@/app/*`: routing, layouts, and page-specific logic
- `@/components/ui/*`: low-level, atomic UI components
- `@/lib/*`: third-party library configuration and shared utilities
- `@/actions/*`: server actions for data mutations, organized by domain
- `@/hooks/*`: reusable client-side custom hooks
- `@/types/*`: global TypeScript definitions and interfaces

Follow this mapping when creating new files.

## Patterns & conventions

### Client component wrapper pattern

Keep the data-fetching server component and wrap it with a client component for interactivity.

Example:

```jsx
"use client";
import { useState } from "react";

function ClientWrapper(props) {
  const { children } = props;
  const [visible, setVisible] = useState(true);

  if (!visible) return null;

  return (
    <div>
      {children}
      <button onClick={() => setVisible(false)}>Dismiss</button>
    </div>
  );
}

function Page() {
  return (
    <ClientWrapper>
      <ServerComponent />
    </ClientWrapper>
  );
}
```

### Pattern "Show More" (server/client composition)

Keep the server component pure and put the interactive logic in a client component.

Example:

```jsx
// Server component
async function CategoryList() {
  const categories = await getCategories();
  return (
    <ShowMore initial={5}>
      {categories.map((category) => (
        <div key={category.id}>{category.name}</div>
      ))}
    </ShowMore>
  );
}

// Client component (ShowMore)
("use client");
import { useState, Children } from "react";

export default function ShowMore(props) {
  const { children, initial = 5 } = props;
  const [expanded, setExpanded] = useState(false);
  const items = expanded
    ? children
    : Children.toArray(children).slice(0, initial);
  const remaining = Children.count(children) - initial;
  return (
    <div>
      <div>{items}</div>
      {remaining > 0 && (
        <button onClick={() => setExpanded(!expanded)}>
          {expanded ? "Show Less" : `Show More (${remaining})`}
        </button>
      )}
    </div>
  );
}
```

## Code quality & delivery checklist

Before finalizing changes, verify:

- Build/lint/type expectations from repository instructions are respected.
- No unnecessary files, abstractions, or visual redesign were introduced.
- Accessibility and responsive behavior are preserved.
- New code follows naming, typing, and folder conventions.
- For every route touched, `loading.tsx` exists at the same segment level as `page.tsx`.
- For every newly introduced significant visual component/block in a route, a corresponding skeleton exists in that route-level `loading.tsx`.
- On incremental updates, route-level skeletons are updated to stay structurally aligned with the current page composition.
- If UI core components changed, the change log file is updated.

## Context7 MCP

For anything not explicitly covered here or in task instructions, use Context7 documentation. When in doubt, choose the simplest compliant solution and avoid overengineering.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/lorenignicloud) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
