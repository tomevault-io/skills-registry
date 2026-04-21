---
name: go-router-navigation
description: Defines navigation and routing patterns using go_router. Use when adding routes, redirects, or navigation logic. Use when this capability is needed.
metadata:
  author: code-sinobi
---

# GoRouter Navigation Skill

This skill standardizes routing for the app.

## When to use this skill

- Adding a new screen
- Implementing auth guards
- Navigating between features

## Routing rules

- All routes defined in `core/router`
- Use named routes
- Prefer declarative navigation

## Auth-aware routing

- Redirect unauthenticated users
- Avoid navigation logic inside widgets
- Read auth state from a provider

## Parameters

- Use path params for IDs
- Use query params for filters

## Anti-patterns

- Pushing routes directly with Navigator
- Hardcoded route strings in widgets

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/code-sinobi) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
