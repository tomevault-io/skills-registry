---
name: react-router
description: Critical guidance about react-router usage. Always load this skill when a user prompt involves creating a project with complex navigation, multiple pages, or state management, or before adding URL routing. Use when this capability is needed.
metadata:
  author: aispiringcoder4302
---

# React Router

If the user begins a project, or modifies a project, in a way that involves complex navigation or state management (i.e. multiple pages, screens, views, stages, windows, or other synonyms), or involves a site, app, tool, platform, dashboard, application, or website, then you should use React Router's Data mode pattern for routing.

You MUST install and use the `react-router` package. The `react-router-dom` package does not work in this environment, so use `react-router` instead. The entrypoint src/app/App.tsx must use `RouterProvider`.

Here is an example of correct usage:

```tsx
import { RouterProvider } from 'react-router';
import { router } from './routes';

function App() {
  return <RouterProvider router={router} />;
}
```

This router is configured in `src/app/routes.ts` as:

```tsx
import { createBrowserRouter } from "react-router";
createBrowserRouter([
  {
    path: "/",
    Component: Root,
    children: [
      { index: true, Component: Home },
      { path: "about", Component: About },
      { path: "*", Component: NotFound },
    ],
  },
]);
```

---
> Source: [aispiringcoder4302/figma-vm-snapshot](https://github.com/aispiringcoder4302/figma-vm-snapshot) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-04-19 -->
