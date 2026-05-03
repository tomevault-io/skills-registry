---
name: frontend-design
description: Create distinctive, production-grade frontend interfaces with high design quality. Use when building web components, pages, applications, or styling/beautifying any web UI. Generates creative, polished code following Design Token methodology and progressive disclosure architecture. Use when this capability is needed.
metadata:
  author: geermrc
---

# Frontend Design Agent Skills

> 📋 **版本**: v0.1.1.1（首个稳定版）
> 🎯 **目标**: 符合Agent Skills最佳实践，功能超越GLM原版

---

## 🎯 核心理念

本技能采用**渐进式披露三层架构（PDA Pattern）**，提供高质量的前端设计指导：

1. **极简入口点** - SKILL.md ≤ 200行（社区黄金标准）
2. **按需加载** - references/ 详细文档按需读取
3. **技术栈灵活** - 支持多框架（React/Vue/Svelte/Angular）
4. **设计优先** - Design Token方法论优先

---

## 🚀 快速开始

### 触发模式

**必须使用场景**：网站/Web应用开发、UI设计、组件库、设计改进、指定框架（React/Vue/Svelte等）、指定工具（Tailwind/shadcn/ui等）

**关键词**："构建网站"、"创建仪表板"、"设计UI"、"让它现代/简洁"

**不用于**：后端API、纯逻辑、非视觉任务

### 设计思维流程

在编写代码前，按以下步骤思考：

1. **理解上下文** - 用途是什么？谁使用？技术约束？
2. **选择美学方向** - 选择大胆、独特的设计风格（brutalist、retro-futuristic、luxury、playful、editorial等）
3. **确定核心差异** - 什么是令人难忘的？
4. **实现高质量代码** - 生产级、视觉震撼、细节精致

**关键原则**：
- 选择清晰的概念方向，精确执行
- 大胆的极繁主义和精致的极简主义都可行
- 关键是有意性，不是强度

### 技术栈默认值

**默认技术栈**（如未指定）：
- 框架：React + TypeScript
- 样式：Tailwind CSS
- 主题：CSS自定义属性（light/dark模式）

**支持的替代方案**：
- 框架：Vue、Svelte、Angular、vanilla HTML/CSS
- 样式：CSS Modules、SCSS、Styled Components、Emotion
- 组件库：MUI、Ant Design、Chakra UI、Headless UI

---

## 📚 文档导航

### 核心方法论文档

| 文档 | 说明 |
|------|------|
| [Design Token方法论](references/methodology/design-tokens.md) | 核心设计令牌系统 |
| [令牌工作流](references/methodology/token-workflow.md) | 令牌开发流程 |
| [系统化方法](references/methodology/systematic-approach.md) | 完整设计系统 |

### 实现指南

| 文档 | 说明 |
|------|------|
| [组件状态覆盖](references/implementation/component-states.md) | 8种状态完整覆盖（1主文档+10份专题） |
| [无障碍指南](references/implementation/accessibility.md) | WCAG AA标准 ✅ DONE |
| [响应式设计](references/implementation/responsive-design.md) | 移动优先 ✅ DONE |
| [性能优化](references/implementation/performance-optimization.md) | 性能最佳实践 |
| [SEO指南](references/implementation/seo-best-practices.md) | 搜索引擎优化 |

### 美学指导

| 文档 | 说明 |
|------|------|
| [设计方向](references/aesthetics/design-directions.md) | 5种设计方向模板（1主文档+9份专题） |
| [排版指南](references/aesthetics/typography.md) | 字体选择与排版 ✅ DONE |
| [色彩理论](references/aesthetics/color-theory.md) | 色彩系统 ✅ DONE |
| [反模式](references/aesthetics/anti-patterns.md) | 避免常见错误 ✅ DONE |

### 质量保证

| 文档 | 说明 |
|------|------|
| [质量清单](references/quality/checklist.md) | 完整检查清单 |
| [审查标准](references/quality/review-criteria.md) | 代码审查标准 ✅ DONE |
| [测试策略](references/quality/testing-strategy.md) | 测试方法 ✅ DONE |

### 示例文档

| 文档 | 说明 |
|------|------|
| [组件示例](references/examples/component-examples.md) | 组件示例（1主文档+2份专题） ✅ DONE |
| [布局示例](references/examples/layout-examples.md) | 布局示例 ✅ DONE |
| [动画示例](references/examples/animation-examples.md) | 动画示例 ✅ DONE |

### 框架特定

| 文档 | 说明 |
|------|------|
| [React](references/by-framework/react.md) | React最佳实践 ✅ DONE |
| [Vue](references/by-framework/vue.md) | Vue最佳实践 |
| [Svelte](references/by-framework/svelte.md) | Svelte最佳实践 |
| [Angular](references/by-framework/angular.md) | Angular最佳实践 |
| [Tailwind](references/by-framework/tailwind.md) | Tailwind配置 ✅ DONE |
| [CSS Modules](references/by-framework/css-modules.md) | CSS Modules指南 ✅ DONE |
| [Styled Components](references/by-framework/styled-components.md) | Styled Components指南 ✅ DONE |

---

## 🎨 前端美学指南

### 核心原则

1. **排版** - 选择独特、美观的字体，避免Inter/Roboto/Arial等通用字体
2. **色彩与主题** - 使用CSS变量，主色调+强烈强调色
3. **动效** - 使用动画实现微交互和页面加载效果
4. **空间构图** - 非对称布局、重叠、对角线流动
5. **背景与细节** - 渐变网格、噪点纹理、几何图案

### 禁止事项

❌ 通用AI美学：
- 滥用的字体系列（Inter、Roboto、Arial）
- 陈词滥调的配色方案（特别是白色背景上的紫色渐变）
- 可预测的布局和组件模式
- 缺乏上下文特定性的千篇一律设计

✅ 正确做法：
- 每个设计都应该是独特的
- 在浅色和深色主题、不同字体、不同美学之间变化
- 避免收敛到通用选择（如Space Grotesk）

---

## 🔧 工具与脚本

### 验证工具
- `scripts/validate/check-tokens.py` - Token验证
- `scripts/validate/check-accessibility.py` - 无障碍检查
- `scripts/validate/check-performance.py` - 性能检查

### 生成工具
- `scripts/generate/generate-theme.py` - 主题生成
- `scripts/generate/generate-component.py` - 组件生成

### 测试工具
- `release/verify/test/test-skill.py` - 技能测试

---

## 📦 项目模板

提供预配置的项目模板：
- `templates/react/` - React项目模板
- `templates/vue/` - Vue项目模板
- `templates/vanilla/` - 原生HTML/CSS/JS模板

---

## ✅ 质量标准

- 代码覆盖率 > 80%
- WCAG AA 无障碍标准
- 移动优先响应式设计
- 性能优化（LCP < 2.5s）
- SEO最佳实践

---

## 📖 更多文档

- [开发流程规范](docs/DEVELOPMENT_WORKFLOW.md)
- [贡献指南](docs/CONTRIBUTING.md)
- [架构说明](docs/ARCHITECTURE.md)
- [API文档](docs/API.md)
- [迁移指南](docs/MIGRATION_GUIDE.md)

---

> **版本**: v0.1.1 | **状态**: ✅ 稳定发布 |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/geermrc) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
