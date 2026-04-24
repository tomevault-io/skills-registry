---
name: refine-dev
description: Refine.dev headless React framework for CRUD apps: data providers, resources, routing, authentication, hooks, and forms. Use when building admin panels, dashboards, or internal tools with React and various backends (REST, GraphQL, Supabase, Strapi), or configuring Refine data providers and authentication. Keywords: Refine, CRUD, admin panel, data provider, React. Use when this capability is needed.
metadata:
  author: itechmeat
---

# Refine.dev Framework

Refine is a headless React framework for building enterprise CRUD applications. It provides data fetching, routing, authentication, and access control out of the box while remaining UI-agnostic.

## Core Concepts

Refine is built around these key abstractions:

1. **Data Provider** — adapter for your backend (REST, GraphQL, etc.)
2. **Resources** — entities in your app (e.g., `posts`, `users`, `products`)
3. **Hooks** — `useList`, `useOne`, `useCreate`, `useUpdate`, `useDelete`, `useForm`, `useTable`
4. **Auth Provider** — handles login, logout, permissions
5. **Router Provider** — integrates with React Router, etc.

## Quick Start (Vite)

Scaffold: `npm create refine-app@latest` (select Vite, Mantine, REST API). For manual setup, install `@refinedev/core @refinedev/mantine @refinedev/react-router` and Mantine packages.

## Minimal App Structure

```tsx
// src/App.tsx
import { Refine } from "@refinedev/core";
import { MantineProvider } from "@mantine/core";
import routerProvider from "@refinedev/react-router";
import dataProvider from "@refinedev/simple-rest";
import { BrowserRouter, Routes, Route } from "react-router-dom";

function App() {
  return (
    <BrowserRouter>
      <MantineProvider>
        <Refine
          dataProvider={dataProvider("https://api.example.com")}
          routerProvider={routerProvider}
          resources={[
            {
              name: "posts",
              list: "/posts",
              create: "/posts/create",
              edit: "/posts/edit/:id",
              show: "/posts/show/:id",
            },
          ]}
        >
          <Routes>{/* Your routes here */}</Routes>
        </Refine>
      </MantineProvider>
    </BrowserRouter>
  );
}
```

## Critical Prohibitions

- Do NOT mix multiple UI libraries (pick Mantine and stick with it)
- Do NOT bypass data provider — always use Refine hooks for data operations
- Do NOT hardcode API URLs — use data provider configuration
- Do NOT skip resource definition — all CRUD entities must be declared in `resources`
- Do NOT ignore TypeScript types — Refine is fully typed, leverage it

## Steps for New Feature

1. Define the resource in `<Refine resources={[...]}>`
2. Create page components (List, Create, Edit, Show)
3. Set up routes matching resource paths
4. Use appropriate hooks (`useTable` for lists, `useForm` for create/edit)
5. Configure auth provider if authentication is needed

## Definition of Done

- [ ] Resource defined in Refine configuration
- [ ] All CRUD pages implemented with proper hooks
- [ ] Routes match resource configuration
- [ ] TypeScript types for resource data defined
- [ ] Error handling in place
- [ ] Loading states handled

## References (Detailed Guides)

### Core

- [data-providers.md](references/data-providers.md) — Data provider interface, available providers, custom implementation
- [resources.md](references/resources.md) — Resource definition and configuration
- [routing.md](references/routing.md) — React Router integration and route patterns

### Hooks

- [hooks.md](references/hooks.md) — All hooks: useList, useOne, useCreate, useUpdate, useDelete, useForm, useTable, useSelect

### Security & Auth

- [auth.md](references/auth.md) — Auth provider, access control, RBAC/ABAC, Casbin/CASL integration

### UI & Components

- [mantine-ui.md](references/mantine-ui.md) — Mantine components integration
- [inferencer.md](references/inferencer.md) — Auto-generate CRUD pages from API schema

### Utilities & Features

- [notifications.md](references/notifications.md) — Notification provider and useNotification hook
- [i18n.md](references/i18n.md) — Internationalization with i18nProvider
- [realtime.md](references/realtime.md) — LiveProvider for websocket/realtime subscriptions

## Links

- [Documentation](https://refine.dev/docs/)
- [Releases](https://github.com/refinedev/refine/releases)
- [GitHub](https://github.com/refinedev/refine)
- [npm](https://www.npmjs.com/package/@refinedev/core)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/itechmeat) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
