---
name: vue-conventions
description: Use when creating, modifying, or reviewing Vue 3 Single-File Components, Composition API code, `<script setup>`, props, emits, slots, or component naming.
metadata:
  author: otwld
---

# Output Marker

Display:
using skill: vue-conventions

---

# Vue Conventions

## Overview

Apply Vue 3 conventions when the target project uses Vue. Follow local lint and
style rules first, then prefer concise Single-File Components with typed
Composition API patterns.

## Core Rules

- Prefer Single-File Components with `<script setup>` for new Vue 3 components.
- Use `lang="ts"` when the project is typed.
- Declare props and emits explicitly, preferably with TypeScript types.
- Use multi-word component names except for the root app component.
- Keep prop casing consistent with the project: camelCase in script, project-approved casing in templates.
- Keep template, script, and style blocks focused and small.

## Usage Checklist

- Vue version and local style were checked.
- Props and emits are explicit.
- Component name is multi-word.
- TypeScript is used when the project is typed.
- Template casing follows local convention.

## Cross-References

- WITH: typescript-code-style, rxjs-conventions, code-documentation
- AFTER: skill-evolution

---
> Source: [otwld/cortex-skills](https://github.com/otwld/cortex-skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
