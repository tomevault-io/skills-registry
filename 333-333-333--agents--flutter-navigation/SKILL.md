---
name: flutter-navigation
description: > Use when this capability is needed.
metadata:
  author: 333-333-333
---

## When to Use

- Adding a new route/screen
- Protecting routes behind authentication
- Setting up bottom navigation with shell routes
- Implementing deep links
- Passing parameters between screens

## Router Setup

Full GoRouter configuration with public routes, auth redirect, and `StatefulShellRoute` for bottom navigation.

> **Example**: See [assets/app_router.dart](assets/app_router.dart)

## Auth Guard — Global Redirect

Redirect function that checks auth state and routes accordingly:
- Not authenticated + protected route → `/login?redirect=...`
- Authenticated + auth route → `/home` (or stored redirect)

> **Example**: See [assets/auth_guard.dart](assets/auth_guard.dart)

## Route Patterns

Four common patterns: simple route, path parameter, query parameter, and nested routes.

> **Examples**: See [assets/route_patterns.dart](assets/route_patterns.dart)

## Shell Routes — Bottom Navigation

`StatefulShellRoute` preserves state across tabs (each tab keeps its own navigation stack).

> **Example**: See [assets/main_shell.dart](assets/main_shell.dart)

## Navigation in Widgets

Use `context.go()`, `context.push()`, and `context.pop()` for declarative navigation.

> **Example**: See [assets/navigation_commands.dart](assets/navigation_commands.dart)

## Connecting Router to MaterialApp

Use `MaterialApp.router` with `routerConfig` from a Riverpod provider.

> **Example**: See [assets/app_entry.dart](assets/app_entry.dart)

## Route Organization by Feature

| Feature | Routes |
|---------|--------|
| Auth | `/login`, `/register`, `/forgot-password` |
| Home | `/home` |
| Booking | `/bookings`, `/bookings/:id`, `/bookings/new` |
| Pet | `/pet/:petId`, `/pet/:petId/edit`, `/pet/:petId/medical` |
| Caregiver | `/caregivers`, `/caregivers/:id` |
| Profile | `/profile`, `/profile/edit` |
| Chat | `/chat`, `/chat/:chatId` |

## Naming Conventions

| Element | Pattern | Example |
|---------|---------|---------|
| Route path | kebab-case | `/forgot-password` |
| Path parameter | camelCase | `:petId`, `:chatId` |
| Query parameter | camelCase | `?serviceType=walk` |
| Router provider | `routerProvider` | Single global instance |

## Anti-Patterns

| ❌ Don't | ✅ Do |
|----------|-------|
| Navigator.push (old API) | `context.go()` or `context.push()` |
| Hardcode auth check in every page | Use global redirect in GoRouter |
| Create router outside Riverpod | Use `Provider<GoRouter>` for reactivity |
| Deep nest routes beyond 3 levels | Flatten with path parameters |
| Pass complex objects via route | Pass IDs, fetch data in the page via provider |
| Forget to handle redirect param on login | Store and restore redirect after auth |

## Resources

- **Templates**: See [assets/](assets/) for router setup, guards, shell, and route patterns

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/333-333-333) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
