---
name: vue-component-review
description: 审查 Vue 组件质量，关注模板结构、Composition API、响应式数据、Props/Emits、生命周期、副作用清理、组件拆分和可测试性；当用户要求审查 Vue 文件、组件质量或组件设计时使用。 Use when this capability is needed.
metadata:
  author: 535803710
---

# Vue Component Review

## 检查重点

- Props 和 Emits 是否表达清楚，默认值和校验是否合理
- 响应式数据是否正确使用，避免无意义的深层监听
- computed、watch、生命周期是否有清晰职责
- 副作用是否能清理，例如定时器、监听器、请求状态
- 模板是否过重，是否需要拆分子组件
- 表单、弹窗、表格状态是否容易出现回显或重置问题
- 组件是否容易测试，外部依赖是否可 mock

## 不负责

- 纯业务算法细节：交给 `code-quality-review`
- 视觉规范和页面体验：交给 `page-review`

## 输出要求

优先指出会导致状态错乱、内存泄露、权限遗漏、测试困难的问题。不要只做风格点评。

---
> Source: [535803710/ai-rd-skills](https://github.com/535803710/ai-rd-skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-24 -->
