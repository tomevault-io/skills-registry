---
name: vue
description: > Use when this capability is needed.
metadata:
  author: PetrovC
---

# Vue Skill

## Goal
Produce clean, maintainable Vue 3 code using the Composition API.
Components should be small and focused. Business logic belongs in composables or stores,
not in `<template>` expressions or component `<script setup>` blocks.

## Quick reference

| Concept | Best practice |
|---|---|
| API Style | Use Composition API with `<script setup>` and TypeScript |
| Reactivity | Use `ref()` for primitives, `reactive()` for objects, `computed()` for derived |
| Props/Emits | Use `defineProps`, `defineEmits`, and `defineModel` (Vue 3.4+) |
| State | Use Pinia for global state; avoid mutating state directly from components |
| Key commands | `npm run dev`, `npm run build`, `npm run test:unit`, `npx vue-tsc --noEmit` |

## Full guidance
Extended how-to, patterns, anti-patterns, and checklists: [`SKILL.deep.md`](SKILL.deep.md)

---
> Source: [PetrovC/ai-agent-kit](https://github.com/PetrovC/ai-agent-kit) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-10 -->
