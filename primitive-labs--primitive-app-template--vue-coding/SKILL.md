---
name: vue-coding
description: > Use when this capability is needed.
metadata:
  author: Primitive-Labs
---

# Vue & TypeScript Coding Standards

These are the coding standards for Vue and TypeScript in this project. Apply them whenever writing
or reviewing code.

## Vue Component Patterns

- ALWAYS use Composition API + `<script setup>`, NEVER use Options API
- ALWAYS place Vue lifecycle methods (e.g. `onMounted`) as the first functions in the component
- ALWAYS use named functions when declaring methods, use arrow functions only for callbacks
- ALWAYS prefer named exports over default exports
- AVOID watch/watchEffect wherever possible. PREFER to call code directly after a user action or after loading data.
- AVOID writing business logic in Vue components. Components should be focused on rendering and UI interaction — move data manipulation and business logic to a related `/lib` file so it can be tested.
- ALWAYS make customizations at the layout level, not in App.vue.

## TypeScript Conventions

- ALWAYS keep types alongside your code, use TypeScript for type safety
- PREFER `interface` over `type` for defining object shapes

## Pinia State Management

- ALWAYS use Pinia for state management. Pinia stores should expose:
  - **State** – refs/reactive objects that can be returned
  - **Getters** – derived, reactive values from state
  - **Actions** – functions that do stuff (async, mutations, side effects)
- AVOID writing exported functions in Pinia stores that return non-reactive state. Helper functions should be internal, actions can return non-reactive status, but shouldn't return non-reactive state. Use reactive getters instead.

## Code Organization

- ALWAYS organize functions in code files in a logical order (e.g. "initialize" functions at the top of the file, a logical sequence or grouping, etc.). Add comments to break up sections of related functions.
- PREFER keeping components and source files under 500 lines of code. ALWAYS refactor code into components if files get large.

---
> Source: [Primitive-Labs/primitive-app-template](https://github.com/Primitive-Labs/primitive-app-template) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-04-19 -->
