---
name: vue-best-practices
description: Use for all Vue.js tasks. Vue 3 + TypeScript best practices — predictable state, explicit data flow, small focused components, SFC structure, reactivity patterns, component splitting triggers. Use when this capability is needed.
metadata:
  author: AlexanderOpran
---

# Vue Best Practices Workflow

Follow this workflow in order for every Vue task.

## Core Principles
- **Keep state predictable:** one source of truth, derive everything else
- **Make data flow explicit:** Props down, Events up
- **Favor small, focused components:** easier to test, reuse, maintain
- **Avoid unnecessary re-renders:** use computed and watchers wisely
- **Readability counts:** write clear, self-documenting code

## 1) Architecture
- Default: Vue 3 + Composition API + `<script setup lang="ts">`
- Plan component boundaries before coding for non-trivial features
- Define each component's single responsibility in one sentence
- Keep entry/root/route components as thin composition surfaces

## 2) Essential Foundations

### Reactivity
- Keep source state minimal (`ref`/`reactive`), derive with `computed`
- Use watchers only for side effects
- Avoid recomputing expensive logic in templates

### SFC Structure
- Order: `<script>` → `<template>` → `<style>`
- Keep templates declarative; move branching to script
- Split when components have multiple responsibilities

### Component Splitting Triggers (split if ANY is true)
- Owns both orchestration/state AND substantial UI for multiple sections
- Has 3+ distinct UI sections
- A template block is repeated or reusable

### Data Flow
- Props down, events up as primary model
- `v-model` only for true two-way contracts
- `provide/inject` only for deep dependencies
- Keep contracts explicit with `defineProps`, `defineEmits`, `InjectionKey`

### Composables
- Extract for: reuse, stateful ops, side effects
- Keep APIs small, typed, predictable
- Separate feature logic from presentational components

## 3) Optional Features (only when needed)
- Slots, KeepAlive, Teleport, Suspense, Transition
- Directives, async components, render functions, plugins

## 4) Performance (after behavior is correct)
- Large list virtualization
- v-once/v-memo for static subtrees
- Avoid component abstraction in hot list paths

## 5) Self-Check
- Core behavior works
- Reactivity minimal and predictable
- SFC structure followed
- Components focused and well-factored
- Data flow explicit and typed
- Composables used where justified
- Performance applied only after functionality complete

---
> Source: [AlexanderOpran/nuxt-build-issue](https://github.com/AlexanderOpran/nuxt-build-issue) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-04-19 -->
