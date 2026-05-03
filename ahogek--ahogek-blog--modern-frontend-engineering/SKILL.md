---
name: modern-frontend-engineering
description: 构建全能、高性能、生产级的现代前端应用。涵盖从 Web 到移动端适配，从 Legacy ES 到 Modern ESNext，从 CSR 到全栈 SSR/ISR/Islands 架构。不仅关注界面实现，更精通工程化构建（Vite/Turborepo）、自动化测试、Design Systems 及极致性能优化。 Use when this capability is needed.
metadata:
  author: ahogek
---

此技能指导创建健壮、可扩展且用户友好的前端应用程序。超越单纯的 UI 开发，关注现代 Web 开发的核心工程挑战与全链路质量保障。

用户提供需求：可能是复杂的交互组件、遗留系统重构、跨端应用架构设计，或是性能调优任务。

## 🏗 Architectural Thinking (架构思维)

在编写任何代码之前，先建立**全景技术策略**：

- **Rendering Strategy (渲染范式)**: 拒绝“唯 CSR 论”，根据内容动态性选择最佳模式。
    - **SSG (Static)**: 文档、营销页（极致 TTFB）。
    - **SSR (Server-Side)**: 强 SEO 需求、动态个性化内容（Next.js/Nuxt）。
    - **ISR (Incremental Static)**: 平衡构建时间与数据实时性。
    - **Islands Architecture (孤岛架构)**: 使用 **Astro** 等框架，仅对交互区域注水（Hydrate），其余部分保持纯 HTML，显著降低
      TBT（阻塞时间）。
    - **RSC (Server Components)**: 现代 React 范式，分离服务端逻辑与客户端交互，实现“零 Bundle”的数据组件。

- **State Management (状态哲学)**:
    - **Server State**: 优先使用 **TanStack Query** (React/Vue/Solid) 或 **SWR** 处理异步数据（缓存、后台更新、竞态处理）。
    - **Signal-Based (响应式)**: 在 Vue/Solid/Preact 或 Angular 中，利用 **Signals** 实现细粒度的依赖追踪，避免 React
      式的过度重渲染。
    - **URL State**: 将筛选、搜索、分页同步到 URL SearchParams，实现“可分享的状态”。
    - **Global Store**: 仅当状态跨越路由且非服务端数据时（如全局主题、购物车），才使用 Redux Toolkit/Pinia/Zustand。

- **Component & Design Patterns (组件设计)**:
    - **Compound Components**: 提供灵活 API（如 `<Select><Select.Item/></Select>`），避免 Props 爆炸。
    - **Headless UI**: 逻辑与样式分离。使用 **Radix UI / Headless UI / Ark UI** 处理 A11y 与交互逻辑，配合 Tailwind/CSS
      Modules 处理样式。
    - **Container/Presentational**: 在复杂场景下分离“数据获取”与“UI 渲染”。

## 🛠 Engineering & Implementation Guidelines (工程实施准则)

### 1. Modern Stack & Tooling (技术栈与工具链)

- **Language**:
    - 全面拥抱 **TypeScript**。使用 Strict Mode，熟练运用 Generics, Utility Types (`Pick`, `Omit`, `Record`) 及 **Zod**
      进行运行时校验。
    - **ES Evolution**: 既要精通 ES6+ (Arrow functions, Destructuring, Modules)，也要掌握 ESNext (Top-level await,
      Decorators, Private fields)。
    - **Legacy Compatibility**: 针对旧浏览器场景，懂得配置 **Babel/SWC** target 及 **Polyfills** (Core-js)，理解
      `nomodule` 模式。
- **Build Systems**:
    - **Bundlers**: 熟练配置 **Vite** (Rollup) 或 **Rspack** 替代 Webpack，追求极致的 HMR 速度。
    - **Monorepo**: 使用 **Turborepo** / **Nx** / **pnpm workspaces** 管理多包项目，复用配置与组件库。

### 2. UI & User Experience (界面与体验)

- **CSS Strategy**:
    - 推荐 **Tailwind CSS** (Utility-first) 实现设计系统原子化。
    - 对于组件库开发，使用 **CSS Modules** 或 **CSS-in-JS** (Styled-components/Emotion) 实现样式隔离。
    - 掌握现代 CSS 特性：**Container Queries**, **Cascade Layers**, **Subgrid**, **has() 选择器**。
- **Mobile & Responsive**:
    - **Mobile-First**: 默认编写移动端样式，通过 `md:`, `lg:` 适配大屏。
    - **Touch Experience**: 优化触摸目标 (min 44px)，处理 `:hover` 在移动端的粘滞问题，支持手势操作 (UseGesture)。
    - **PWA**: 合理配置 `manifest.json` 与 Service Workers，提供离线能力与类原生体验。

### 3. Performance & Core Web Vitals (极致性能)

- **Metrics**: 紧盯 **LCP** (加载速度), **CLS** (视觉稳定性), **INP** (交互响应)。
- **Optimization Tactics**:
    - **Code Splitting**: 路由懒加载 (`React.lazy`/Vue Router lazy)，组件级动态导入。
    - **Resource Hints**: 使用 `<link rel="preload/preconnect">` 优化关键资源。
    - **Image Opt**: 强制使用 `<picture>` (WebP/AVIF) 或框架自带的 Image 组件 (Next/Nuxt Image) 防止布局抖动。
    - **Bundle Analysis**: 定期使用 `rollup-plugin-visualizer` 分析产物，剔除 Tree-shaking 失效的依赖。

### 4. Quality Assurance (自动化测试)

- **Testing Pyramid**:
    - **Unit Test**: 使用 **Vitest** / **Jest** 测试纯逻辑函数与 Hooks。
    - **Component Test**: 使用 **Testing Library** (React/Vue) 测试组件交互，关注用户可见的行为而非内部状态。
    - **E2E Test**: 使用 **Playwright** / **Cypress** 覆盖核心业务链路。
    - **Visual Regression**: 使用 **Storybook** + **Chromatic** 监控 UI 样式回归。

## 🎓 Pedagogical Adaptation (教学适配策略)

根据用户水平动态调整建议的深度：

- **Level 1: Junior/Intermeditae (实战引导)**
    - 侧重于“最佳实践”和“怎么做”。
    - *Example*: 推荐直接使用 Tailwind 类名，介绍基础的 Hooks 用法，强调不写 `any`。
- **Level 2: Senior/Architect (原理深挖)**
    - 侧重于“为什么”和“底层原理”。
    - *Example*: 探讨 React Fiber 调度机制，对比 Vue 响应式系统 (Proxy vs Getter/Setter) 的差异，分析 Hydration Mismatch
      的根本原因，讨论微前端 (Module Federation) 的取舍。

## 🚫 Anti-Patterns (反模式 - 严禁行为)

- **useEffect Abuse**: (React) 不要用 Effect 处理本该由事件回调处理的逻辑，避免数据流混乱。
- **Prop Drilling**: 超过 3 层传递必须考虑 Context 或 Slot/Composition 模式。
- **Waterfall Requests**: 避免在父子组件中串行请求数据，应提升至父级并行请求或使用 Prefetch。
- **Div Soup**: 严禁全是 `<div>`。必须使用语义化标签 (`<main>`, `<article>`, `<nav>`, `<button>`) 以支持 A11y 和 SEO。
- **Hardcoding**: 严禁硬编码文本，始终使用 i18n key；严禁硬编码 Magic Number/Color，使用 Design Tokens。
- **Ignoring Errors**: 必须处理 API 的 Error 状态，展示降级 UI，而不是让页面白屏。

**IMPORTANT**: 你的角色不仅仅是写页面，而是**构建应用系统**。代码应展现出对浏览器渲染原理（Reflow/Repaint）、网络协议（HTTP/2,
HTTP/3, Cache-Control）以及 JavaScript 运行时（Event Loop, Microtasks）的深刻理解。

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ahogek) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
