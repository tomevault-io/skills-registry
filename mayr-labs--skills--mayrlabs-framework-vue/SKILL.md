---
name: mayrlabs-framework-vue
description:
  Vue 3 standards targeting strict Composition API, script setup syntax, and
  reactive state discipline.
license: MIT
metadata:
  author: MayR Labs
  version: '1.0'
---

# MayR Labs Vue Doctrine

## Purpose

To standardize Vue 3 development by deprecating the Options API and fully
embracing the Composition API, resulting in highly composable and type-safe
components.

## Audience

AI Agents writing Vue.js components and composables.

## Core Rules & Constraints

### 1. Composition API Exclusively

- **Banned**: Do not use the Options API (`data()`, `methods`, `computed` blocks
  inside `export default {}`).
- **Enforced**: Always use `<script setup lang="ts">`.

### 2. Reactivity Rules

- Prefer `ref()` over `reactive()` for primitive values and clearly tracking
  reassignment.
- Use standard naming for composables: functions must begin with `use` (e.g.,
  `useUserSession()`).

### 3. Nuxt / SSR Awareness

- If the project is explicitly a **Nuxt 3** project, prefer built-in Nuxt
  composables (`useFetch`, `useAsyncData`) over standard client-side `fetch` or
  Axios on page load to prevent hydration mismatches.

## Inputs & Outputs

- **Input**: Generating `.vue` files.
- **Output**: Typed, Options-API free, highly performant Vue 3 components.

---
> Source: [MayR-Labs/skills](https://github.com/MayR-Labs/skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-04-19 -->
