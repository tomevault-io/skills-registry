---
name: lang-vue
description: Vue.js development standards and tooling. Use when this capability is needed.
metadata:
  author: dragoscirjan
---

# VUE Standards

- **Style:** [Vue Style Guide](https://vuejs.org/style-guide/) (Essential & Strongly Recommended).
- **Tooling:** `Prettier`, `eslint-plugin-vue`, `Vue Test Utils`.
- **Modern Vue:** Use Composition API (`<script setup>`). 
- **State:** Use `ref` for primitives, `reactive` for objects, `computed` for derived state.
- **Components:** Single-File Components (SFC). "Props down, events up" via `defineProps` and `defineEmits`.

Always load `clean-code` alongside this skill.

---
> Source: [dragoscirjan/opencode-config](https://github.com/dragoscirjan/opencode-config) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
