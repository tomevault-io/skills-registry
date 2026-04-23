---
name: vite
description: 使用 Rollup Visualizer 分析产物，并通过拆包 (Code Splitting) 和 Tree Shaking 减少首屏体积。 Use when this capability is needed.
metadata:
  author: tencentblueking
---

# Vite 构建产物体积优化

当首屏加载慢 (FCP > 1.5s) 时，通常需要检查 JS Bundle 的体积。

## 1. 产物分析 (Visualizer)

首先"看见"哪些包最大。

```bash
npm install rollup-plugin-visualizer -D
```

## 2. 常用优化策略

### 路由懒加载 (Route Lazy Loading)
**这是收益最大的优化点**。

❌ `import UserList from './views/UserList.vue'`
✅ `component: () => import('./views/UserList.vue')`

### 依赖分包 (Manual Chunks)
将大型库（ECharts, bkui-vue）单独打包，避免 vendor hash 频繁变化。

### 按需引入 (Tree Shaking)
❌ `import _ from 'lodash'`
✅ `import debounce from 'lodash-es/debounce'`

### Gzip 压缩
```bash
npm install vite-plugin-compression -D
```

## 3. 完整配置模版

> 📦 获取完整 vite.config.ts 配置：`skill://bundle-optimization/assets/vite.config.optimization.ts`


---
## 📦 可用资源

- `skill://bundle-optimization/assets/vite.config.optimization.ts`

> 根据 SKILL.md 中的 IF-THEN 规则判断是否需要加载

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tencentblueking) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
