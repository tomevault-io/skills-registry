---
name: ui-development
description: UI 架构与组件开发规范。涵盖 shadcn/ui 集成、Tailwind CSS 使用、动画标准以及自定义组件原则。 Use when this capability is needed.
metadata:
  author: sumanin5
---

# UI 开发与组件规范

## 🎨 核心技术栈
- **基础库**: React 19 + Next.js 16 (App Router)
- **UI 框架**: [shadcn/ui](https://ui.shadcn.com/) (基于 Radix UI)
- **样式**: Tailwind CSS 4.0
- **图标**: Lucide React
- **动画**: Framer Motion
- **表单**: React Hook Form + Zod

## 🛠️ shadcn/ui 管理规范
对于 shadcn 组件，本项目遵循以下原则：

### 1. 源码即真相
- **不使用技能存储组件列表**: 已安装的组件列表应通过检查 `frontend/src/components/ui/` 目录和 `frontend/components.json` 获取。
- **直接修改**: 本项目鼓励直接修改 `ui/` 目录下的组件源码以满足特定设计需求，而不是在其之上包裹多层。

### 2. 添加与更新
- 使用内置的 **shadcn MCP 工具** 来搜索和添加组件。
- 命令行手动操作: `pnpm dlx shadcn@latest add [component-name]`。

### 3. 组件分类
- `src/components/ui/`: 基础原子组件（shadcn 导出的原始组件）。
- `src/components/shared/`: 跨模块复用的业务组件（如 `CategorySelect`, `PostCard`）。
- `src/components/{module}/`: 模块私有组件（如 `admin/posts/PostEditor`）。

## 👗 样式编码标准

### 1. 响应式优先
- 使用 Tailwind 的响应式前缀 (`sm:`, `md:`, `lg:`)。
- 即使是简单的列表也应考虑移动端适配。

### 2. 交互状态
- 所有的交互元素（按钮、链接）必须定义 `hover`, `focus-visible`, `active` 状态。
- 使用 `ring-offset` 和 `focus-visible:ring-2` 确保无障碍访问。

### 3. 暗黑模式
- 使用 `next-themes` 管理状态。
- 样式中应同时考虑 `light`（默认）和 `dark:`。

## ✨ 交互与动画 (Framer Motion)
- **页面切换**: 使用 `AnimatePresence` 处理进入/退出动画。
- **微交互**: 用于按钮点击、弹出层出现等场景，提升质感。
- **原则**: 动画应快速（通常 < 200ms）且不干扰用户操作流程。

## 🧩 自定义组件开发指引
1. **复合组件模式**: 复杂的组件（如 Select/Combobox）应优先使用 shadcn 的组合模式。
2. **逻辑抽离**: 复杂的表单逻辑或数据请求应抽离到独立的 Hook（如 `use-posts.ts`），组件层只负责 UI。
3. **Props 规范**:
   - 必须定义 TypeScript 接口。
   - 透传基础 HTML 属性（使用 `React.ComponentPropsWithoutRef<"div">` 等）。
   - 使用 `cn()` 工具函数合并 className。

## 🔍 工具使用建议
- 当需要新组件时，首先调用 `mcp_shadcn_search_items_in_registries`。
- 查看示例时，调用 `mcp_shadcn_get_item_examples_from_registries` 获取最佳实践代码。

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sumanin5) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
