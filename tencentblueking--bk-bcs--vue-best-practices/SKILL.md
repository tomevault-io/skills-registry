---
name: vue-best-practices
description: Vue 3 TypeScript, vue-tsc, Volar, Vite, component props, testing, composition API. Use when this capability is needed.
metadata:
  author: tencentblueking
---

# Vue Best Practices

## Capability Rules

| Rule | Keywords | Description |
|------|----------|-------------|
| [vue-tsc-strict-templates](rules/vue-tsc-strict-templates.md) | undefined component, template error, strictTemplates | Catch undefined components in templates |
| [fallthrough-attributes](rules/fallthrough-attributes.md) | fallthrough, $attrs, wrapper component | Type-check fallthrough attributes |
| [strict-css-modules](rules/strict-css-modules.md) | css modules, $style, typo | Catch CSS module class typos |
| [data-attributes-config](rules/data-attributes-config.md) | data-*, strictTemplates, attribute | Allow data-* attributes |
| [volar-3-breaking-changes](rules/volar-3-breaking-changes.md) | volar, vue-language-server, editor | Fix Volar 3.0 upgrade issues |
| [module-resolution-bundler](rules/module-resolution-bundler.md) | cannot find module, @vue/tsconfig, moduleResolution | Fix module resolution errors |
| [unplugin-auto-import-conflicts](rules/unplugin-auto-import-conflicts.md) | unplugin, auto-import, types any | Fix unplugin type conflicts |
| [codeactions-save-performance](rules/codeactions-save-performance.md) | slow save, vscode, performance | Fix slow save in large projects |
| [duplicate-plugin-detection](rules/duplicate-plugin-detection.md) | duplicate plugin, vite, vue plugin | Detect duplicate plugins |
| [define-model-update-event](rules/define-model-update-event.md) | defineModel, update event, undefined | Fix model update errors |
| [with-defaults-union-types](rules/with-defaults-union-types.md) | withDefaults, union type, default | Fix union type defaults |
| [deep-watch-numeric](rules/deep-watch-numeric.md) | watch, deep, array, Vue 3.5 | Efficient array watching |
| [vue-directive-comments](rules/vue-directive-comments.md) | @vue-ignore, @vue-skip, template | Control template type checking |
| [script-setup-jsdoc](rules/script-setup-jsdoc.md) | jsdoc, script setup, documentation | Add JSDoc to script setup |
| [vue-router-typed-params](rules/vue-router-typed-params.md) | route params, typed router, unplugin | Fix route params typing |

## Efficiency Rules

| Rule | Keywords | Description |
|------|----------|-------------|
| [hmr-vue-ssr](rules/hmr-vue-ssr.md) | hmr, ssr, hot reload | Fix HMR in SSR apps |
| [pinia-store-mocking](rules/pinia-store-mocking.md) | pinia, mock, vitest, store | Mock Pinia stores |

## Reference

- [Vue Language Tools](https://github.com/vuejs/language-tools)
- [Vue 3 Documentation](https://vuejs.org/)


---
## 📦 可用资源

- `skill://vue-best-practices/rules/codeactions-save-performance.md`
- `skill://vue-best-practices/rules/data-attributes-config.md`
- `skill://vue-best-practices/rules/deep-watch-numeric.md`
- `skill://vue-best-practices/rules/define-model-update-event.md`
- `skill://vue-best-practices/rules/duplicate-plugin-detection.md`
- `skill://vue-best-practices/rules/fallthrough-attributes.md`
- `skill://vue-best-practices/rules/hmr-vue-ssr.md`
- `skill://vue-best-practices/rules/module-resolution-bundler.md`
- `skill://vue-best-practices/rules/pinia-store-mocking.md`
- `skill://vue-best-practices/rules/script-setup-jsdoc.md`
- `skill://vue-best-practices/rules/strict-css-modules.md`
- `skill://vue-best-practices/rules/unplugin-auto-import-conflicts.md`
- `skill://vue-best-practices/rules/volar-3-breaking-changes.md`
- `skill://vue-best-practices/rules/vue-directive-comments.md`
- `skill://vue-best-practices/rules/vue-router-typed-params.md`
- `skill://vue-best-practices/rules/vue-tsc-strict-templates.md`
- `skill://vue-best-practices/rules/with-defaults-union-types.md`

> 根据 SKILL.md 中的 IF-THEN 规则判断是否需要加载

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tencentblueking) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
