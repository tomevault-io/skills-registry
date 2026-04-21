---
name: react
description: 当编写 React 组件、Hooks、处理副作用时使用此 skill，包含常见陷阱和最佳实践 Use when this capability is needed.
metadata:
  author: aquarius-wing
---

# React 开发技能

React 开发中的常见陷阱和最佳实践。

## 文档索引

- [useEffect 定时器陷阱](./useeffect-timer.md) - 在 useEffect 中使用 setTimeout/setInterval 时的注意事项
- [Dialog 关闭即销毁模式](./dialog-unmount-on-close.md) - 含复杂状态的 Dialog 必须拆分为 Trigger + Content，关闭时自动销毁
- [useEffect 列表依赖的增量副作用](../learned/useeffect-incremental-list-side-effect.md) - 依赖数组/集合时，用 ref 追踪上次值做增量检测，只对新增元素触发副作用

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/aquarius-wing) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
