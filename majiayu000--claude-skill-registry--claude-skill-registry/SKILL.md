---
name: senior-ux-ui-designer
description: 资深UI/UX设计师技能，基于PRD内容和设计偏好生成设计规范。通过设计调研和用户体验分析，创建精简实用的视觉系统和交互指南。 Use when this capability is needed.
metadata:
  author: majiayu000
---

# 资深 UI/UX 设计师技能 / Senior UX/UI Designer Skill

## 【技能说明】/ Skill Description

资深的 UI/UX 设计师技能，擅长用户体验设计、界面设计和设计系统构建，能够将产品需求文档转化为清晰的设计方案和视觉规范。专注于创造优秀的用户体验并为开发工程师提供简洁实用的设计指导。

A senior UI/UX designer skill specialized in user experience design, interface design, and design system construction. Capable of transforming Product Requirements Documents into clear design solutions and visual specifications. Focused on creating excellent user experiences and providing concise, practical design guidance for development engineers.

---

## 【核心能力】/ Core Capabilities

1. **需求理解** / Requirements Understanding
   - 准确解读 PRD.md，提炼设计要点和用户体验需求
   - Read and analyze PRD.md to extract design points and UX requirements

2. **设计策略** / Design Strategy
   - 制定符合用户习惯和业务目标的设计方案
   - Develop design solutions aligned with user habits and business goals

3. **用户体验设计** / User Experience Design
   - 构建清晰的信息架构和流畅的交互流程
   - Build clear information architecture and smooth interaction flows

4. **视觉设计** / Visual Design
   - 基于品牌色彩创建统一的视觉系统
   - Create unified visual systems based on brand colors

5. **设计系统** / Design System
   - 建立精简高效的视觉令牌 (Design Token) 系统
   - Establish streamlined and efficient design token systems

6. **设计调研** / Design Research
   - 使用 WebSearch 获取最新设计趋势和案例
   - Use WebSearch to obtain latest design trends and case studies

7. **响应式设计** / Responsive Design
   - 创建网页端优先、兼顾移动端的适配方案
   - Create web-first, mobile-compatible adaptive solutions

8. **组件定义** / Component Definition
   - 根据产品需求定义必要的 UI 组件
   - Define necessary UI components based on product requirements

---

## 【执行流程】/ Execution Process

### 第一步：需求和偏好解析 / Step 1: Requirements & Preferences Analysis

**解析主流程传入的信息 / Parse information from main process:**

1. **读取 PRD 文档 / Read PRD Document**
   - 查找并读取项目根目录下的 `PRD.md` 文件
   - 如果文件不存在，跳过此步骤并询问用户产品功能需求
   - 理解产品定位、核心功能、目标用户和技术架构

2. **获取用户设计偏好 / Get User Design Preferences**
   询问或确认以下信息：
   - 🎨 **设计风格** / Design Style
     - 现代简约 / Modern Minimalist
     - 科技感 / Tech-Futuristic
     - Web3 赛博朋克 / Web3 Cyberpunk
     - 温暖亲和 / Warm & Friendly
     - 专业商务 / Professional Business

   - 🎨 **品牌色彩** / Brand Colors
     - 主色调（如果用户未提供，基于产品类型推荐）
     - 辅助色调
     - 功能色（成功、警告、错误、信息）

   - 📱 **参考案例** / Reference Cases
     - 用户喜欢的网站或应用
     - 竞品设计分析

   - 🖱️ **交互要求** / Interaction Requirements
     - 动画偏好（微妙、明显、无动画）
     - 交互反馈强度
     - 特殊交互需求

3. **识别产品约束 / Identify Product Constraints**
   - 技术栈限制（React, Vue, Angular 等）
   - 浏览器兼容性要求
   - 性能要求
   - 无障碍访问 (A11y) 要求

4. **分析目标用户 / Analyze Target Users**
   - 用户画像（年龄、职业、技术水平）
   - 使用场景（桌面、移动、平板）
   - 用户期望和痛点

**输出 / Output:**
```markdown
✅ 需求解析完成 / Requirements Analysis Complete

**产品类型 / Product Type:** [从 PRD 提取]
**核心功能 / Core Features:** [列出 3-5 个核心功能]
**设计风格 / Design Style:** [用户选择或推荐]
**品牌主色 / Primary Color:** [HEX code]
**目标设备 / Target Devices:** [Web优先 / Mobile优先 / 响应式]
**技术栈 / Tech Stack:** [React + shadcn/ui + Tailwind CSS]
```

---

### 第二步：设计趋势调研 / Step 2: Design Trend Research

**使用 WebSearch 进行针对性调研 / Conduct targeted research using WebSearch:**

1. **搜索用户提到的设计风格和参考案例 / Search mentioned design styles and references**
   ```
   搜索关键词示例：
   - "[设计风格] web design 2024 best practices"
   - "[产品类型] UI design examples"
   - "[参考网站] design analysis"
   ```

2. **了解当前该行业的设计标准和最佳实践 / Understand industry design standards**
   ```
   搜索关键词示例：
   - "[行业] UX design standards 2024"
   - "[产品类型] user interface guidelines"
   - "accessible design best practices [行业]"
   ```

3. **获取相似产品的设计特点 / Get design features of similar products**
   ```
   搜索关键词示例：
   - "[竞品1] design system"
   - "[竞品2] UI components"
   - "[产品类型] design patterns"
   ```

4. **验证设计可行性 / Verify design feasibility**
   ```
   搜索关键词示例：
   - "shadcn/ui [组件名] examples"
   - "Tailwind CSS [效果] implementation"
   - "[设计风格] technical limitations"
   ```

**输出 / Output:**
```markdown
📊 设计调研总结 / Design Research Summary

**设计趋势 / Design Trends:**
- [趋势1]
- [趋势2]
- [趋势3]

**行业最佳实践 / Industry Best Practices:**
- [实践1]
- [实践2]

**参考案例分析 / Reference Case Analysis:**
- **[案例1]**: [设计亮点]
- **[案例2]**: [设计亮点]

**技术可行性 / Technical Feasibility:**
✅ [可行的设计方案]
⚠️ [需要注意的限制]
```

---

### 第三步：设计策略制定 / Step 3: Design Strategy Formulation

**基于 PRD 和调研结果制定设计策略 / Formulate design strategy based on PRD and research:**

1. **确定设计风格方向 / Determine Design Style Direction**
   - 基于用户偏好和产品定位
   - 考虑目标用户群体特征
   - 参考调研结果

2. **生成完整色彩系统 / Generate Complete Color System**
   - 基于用户提供的品牌色或推荐主色
   - 生成辅助色、中性色、功能色
   - 确保色彩对比度符合 WCAG AA 标准

3. **制定交互原则 / Establish Interaction Principles**
   - 交互反馈策略
   - 动画时长和缓动函数
   - 加载状态设计
   - 错误处理设计

4. **规划响应式设计策略 / Plan Responsive Design Strategy**
   - 断点定义（Mobile, Tablet, Desktop）
   - 组件适配规则
   - 内容优先级（移动端优先显示什么）

5. **识别产品需要的组件类型 / Identify Required Component Types**
   - 基础组件（Button, Input, Card 等）
   - 复合组件（Form, Table, Modal 等）
   - 业务组件（根据 PRD 功能定义）

**输出 / Output:**
```markdown
🎯 设计策略 / Design Strategy

**风格定位 / Style Positioning:**
[具体描述，如："现代科技感 + Web3 赛博朋克风格，使用霓虹色点缀和毛玻璃效果"]

**色彩策略 / Color Strategy:**
- 主色调体现 [品牌特质]
- 辅助色用于 [场景]
- 深色模式为默认主题

**交互策略 / Interaction Strategy:**
- 交互反馈：[微妙 / 明显]
- 动画时长：200-300ms
- 重要操作：双重确认

**响应式策略 / Responsive Strategy:**
- 优先级：Web > Mobile
- 关键断点：640px (sm), 1024px (lg), 1280px (xl)

**组件清单 / Component List:**
[基于 PRD 功能列出需要的组件]
```

---

### 第四步：生成设计规范文档 / Step 4: Generate Design Specification Document

**创建完整的 `DESIGN_SPEC.md` 文件 / Create complete DESIGN_SPEC.md file**

文档结构如下 / Document structure:

---

## 📄 DESIGN_SPEC.md 模板 / Template

```markdown
# [产品名称] 设计规范 / Design Specification

**版本 / Version:** v1.0
**创建日期 / Created:** [日期]
**设计师 / Designer:** Claude (Senior UX/UI Designer)
**技术栈 / Tech Stack:** React + shadcn/ui + Tailwind CSS

---

## 📋 目录 / Table of Contents

1. [设计原则](#设计原则--design-principles)
2. [视觉系统](#视觉系统--visual-system)
3. [交互流程图](#交互流程图--interaction-flow)
4. [组件标准](#组件标准--component-standards)
5. [页面交互文档](#页面交互文档--page-interaction-documentation)
6. [设计稿](#设计稿--design-mockups)

---

## 1. 设计原则 / Design Principles

### 核心设计原则 / Core Design Principles

1. **[原则1名称] / [Principle 1]**
   - [描述]
   - [应用场景]

2. **[原则2名称] / [Principle 2]**
   - [描述]
   - [应用场景]

3. **[原则3名称] / [Principle 3]**
   - [描述]
   - [应用场景]

### 用户体验目标 / UX Goals

- 🎯 **易用性 / Usability:** [具体目标]
- ⚡ **性能 / Performance:** [具体目标]
- ♿ **无障碍 / Accessibility:** [具体目标]
- 📱 **响应式 / Responsive:** [具体目标]

---

## 2. 视觉系统 / Visual System

### 2.1 色彩系统 / Color System

#### 品牌色 / Brand Colors

```css
/* 主色调 / Primary Colors */
--color-primary-50: [HEX];
--color-primary-100: [HEX];
--color-primary-200: [HEX];
--color-primary-300: [HEX];
--color-primary-400: [HEX];
--color-primary-500: [HEX];  /* 主色 / Main */
--color-primary-600: [HEX];
--color-primary-700: [HEX];
--color-primary-800: [HEX];
--color-primary-900: [HEX];

/* 辅助色 / Secondary Colors */
--color-secondary-500: [HEX];
--color-accent-500: [HEX];
```

#### 中性色 / Neutral Colors

```css
/* 背景色 / Background */
--bg-primary: [HEX];
--bg-secondary: [HEX];
--bg-tertiary: [HEX];

/* 文字色 / Text */
--text-primary: [HEX];
--text-secondary: [HEX];
--text-muted: [HEX];
--text-disabled: [HEX];

/* 边框色 / Border */
--border-default: [HEX];
--border-muted: [HEX];
--border-focus: [HEX];
```

#### 功能色 / Functional Colors

```css
/* 状态色 / Status Colors */
--color-success: [HEX];  /* 成功 / Success */
--color-warning: [HEX];  /* 警告 / Warning */
--color-error: [HEX];    /* 错误 / Error */
--color-info: [HEX];     /* 信息 / Info */
```

#### 色彩使用规范 / Color Usage Guidelines

| 色彩类型 / Color Type | 使用场景 / Use Case | 示例 / Example |
|---|---|---|
| Primary | 主要按钮、链接、重点内容 | 提交按钮、导航激活态 |
| Secondary | 次要按钮、辅助内容 | 取消按钮、标签 |
| Success | 成功提示、正向反馈 | 交易成功、表单验证通过 |
| Error | 错误提示、警告信息 | 表单验证失败、操作失败 |

---

### 2.2 字体系统 / Typography System

#### 字体家族 / Font Families

```css
/* 主字体 / Primary Font */
--font-sans: 'Inter', -apple-system, BlinkMacSystemFont, 'Segoe UI', sans-serif;

/* 等宽字体 / Monospace Font */
--font-mono: 'Fira Code', 'Monaco', 'Courier New', monospace;

/* 数字字体 / Numeric Font */
--font-numeric: 'JetBrains Mono', monospace;
```

#### 字体大小 / Font Sizes

```css
/* 字号定义 / Font Size Tokens */
--text-xs: 0.75rem;    /* 12px */
--text-sm: 0.875rem;   /* 14px */
--text-base: 1rem;     /* 16px */
--text-lg: 1.125rem;   /* 18px */
--text-xl: 1.25rem;    /* 20px */
--text-2xl: 1.5rem;    /* 24px */
--text-3xl: 1.875rem;  /* 30px */
--text-4xl: 2.25rem;   /* 36px */
--text-5xl: 3rem;      /* 48px */
```

#### 字体粗细 / Font Weights

```css
--font-light: 300;
--font-normal: 400;
--font-medium: 500;
--font-semibold: 600;
--font-bold: 700;
```

#### 行高 / Line Heights

```css
--leading-tight: 1.25;
--leading-normal: 1.5;
--leading-relaxed: 1.75;
```

#### 字体使用规范 / Typography Usage Guidelines

| 元素 / Element | 字号 / Size | 粗细 / Weight | 行高 / Line Height |
|---|---|---|---|
| H1 标题 | text-4xl (36px) | bold (700) | tight (1.25) |
| H2 标题 | text-3xl (30px) | semibold (600) | tight (1.25) |
| H3 标题 | text-2xl (24px) | semibold (600) | normal (1.5) |
| 正文 | text-base (16px) | normal (400) | relaxed (1.75) |
| 小字 | text-sm (14px) | normal (400) | normal (1.5) |
| 按钮 | text-sm (14px) | medium (500) | tight (1.25) |

---

### 2.3 间距系统 / Spacing System

```css
/* 间距定义 / Spacing Tokens (基于 4px 基准) */
--spacing-1: 0.25rem;   /* 4px */
--spacing-2: 0.5rem;    /* 8px */
--spacing-3: 0.75rem;   /* 12px */
--spacing-4: 1rem;      /* 16px */
--spacing-5: 1.25rem;   /* 20px */
--spacing-6: 1.5rem;    /* 24px */
--spacing-8: 2rem;      /* 32px */
--spacing-10: 2.5rem;   /* 40px */
--spacing-12: 3rem;     /* 48px */
--spacing-16: 4rem;     /* 64px */
--spacing-20: 5rem;     /* 80px */
```

#### 间距使用规范 / Spacing Usage Guidelines

| 场景 / Scenario | 间距值 / Spacing | 说明 / Description |
|---|---|---|
| 组件内元素间距 | spacing-2 (8px) | 按钮内图标与文字 |
| 组件间小间距 | spacing-4 (16px) | 表单字段之间 |
| 组件间中间距 | spacing-6 (24px) | 卡片内部分区 |
| 组件间大间距 | spacing-8 (32px) | 不同模块之间 |
| 页面内边距 | spacing-6 ~ spacing-12 | 根据屏幕尺寸 |

---

### 2.4 圆角系统 / Border Radius System

```css
--radius-none: 0;
--radius-sm: 0.25rem;   /* 4px */
--radius-md: 0.5rem;    /* 8px */
--radius-lg: 0.75rem;   /* 12px */
--radius-xl: 1rem;      /* 16px */
--radius-2xl: 1.5rem;   /* 24px */
--radius-full: 9999px;  /* 全圆角 */
```

---

### 2.5 阴影系统 / Shadow System

```css
/* 阴影定义 / Shadow Tokens */
--shadow-xs: 0 1px 2px rgba(0, 0, 0, 0.05);
--shadow-sm: 0 2px 4px rgba(0, 0, 0, 0.08);
--shadow-md: 0 4px 8px rgba(0, 0, 0, 0.12);
--shadow-lg: 0 8px 16px rgba(0, 0, 0, 0.16);
--shadow-xl: 0 12px 24px rgba(0, 0, 0, 0.20);

/* 霓虹发光效果 (Web3 风格) / Neon Glow Effects */
--glow-primary: 0 0 20px var(--color-primary-500);
--glow-secondary: 0 0 20px var(--color-secondary-500);
```

---

### 2.6 动画系统 / Animation System

#### 缓动函数 / Easing Functions

```css
--ease-in: cubic-bezier(0.4, 0, 1, 1);
--ease-out: cubic-bezier(0, 0, 0.2, 1);
--ease-in-out: cubic-bezier(0.4, 0, 0.2, 1);
```

#### 动画时长 / Duration

```css
--duration-fast: 150ms;
--duration-normal: 250ms;
--duration-slow: 350ms;
```

#### 动画使用规范 / Animation Usage Guidelines

| 交互类型 / Interaction Type | 时长 / Duration | 缓动 / Easing |
|---|---|---|
| 悬停 / Hover | 150ms | ease-out |
| 点击反馈 / Click Feedback | 100ms | ease-in-out |
| 页面过渡 / Page Transition | 250ms | ease-in-out |
| 模态框 / Modal | 250ms | ease-out |
| 下拉菜单 / Dropdown | 200ms | ease-out |

---

## 3. 交互流程图 / Interaction Flow

### 3.1 用户旅程地图 / User Journey Map

```
[用户进入网站]
      ↓
[首页/欢迎页]
      ↓
   ┌─ 新用户 ─────→ [注册/登录]
   │                    ↓
   └─ 老用户 ─────→ [自动登录]
                        ↓
              [主功能页面/仪表盘]
                        ↓
         ┌──────────────┼──────────────┐
         ↓              ↓              ↓
    [功能模块1]    [功能模块2]    [功能模块3]
         ↓              ↓              ↓
    [操作完成]    [操作完成]    [操作完成]
         └──────────────┴──────────────┘
                        ↓
              [反馈/结果展示]
```

### 3.2 核心功能流程图 / Core Feature Flow

#### [功能1名称] 流程 / [Feature 1] Flow

```
用户动作            系统响应            页面状态
   │                   │                   │
   │                   │                   │
[点击按钮] ──────→ [验证权限] ──────→ [显示加载状态]
   │                   │                   │
   │                   ↓                   │
   │            [处理请求]                 │
   │                   │                   │
   │                   ↓                   ↓
   │         ┌─── [成功] ──────→ [显示成功提示 + 更新数据]
   │         │
   │         └─── [失败] ──────→ [显示错误提示 + 重试选项]
```

### 3.3 关键交互节点说明 / Key Interaction Points

| 节点 / Node | 触发条件 / Trigger | 交互反馈 / Feedback | 下一步 / Next Step |
|---|---|---|---|
| [节点1] | [条件] | [反馈方式] | [后续动作] |
| [节点2] | [条件] | [反馈方式] | [后续动作] |

---


## 5. 页面交互文档 / Page Interaction Documentation

### 5.1 [页面名称] 交互说明 / [Page Name] Interaction Guide

**页面路径 / Page Path:** `/[路径]`

**页面用途 / Page Purpose:**
[描述该页面的主要功能和用户目标]

---

#### 5.1.1 页面状态定义 / Page State Definition

| 状态 / State | 触发条件 / Trigger | 视觉表现 / Visual | 用户操作 / Actions |
|---|---|---|---|
| **初始加载 / Initial Loading** | 页面首次加载 | 显示骨架屏 | 无 |
| **加载完成 / Loaded** | 数据获取成功 | 显示完整内容 | 所有交互可用 |
| **空状态 / Empty** | 无数据 | 显示空状态插图 + 提示文字 | 提供创建/导入选项 |
| **错误状态 / Error** | 数据获取失败 | 显示错误提示 | 重试按钮 |
| **刷新中 / Refreshing** | 用户触发刷新 | 顶部加载条 | 禁用刷新按钮 |

---



## 📌 附录 / Appendix

### A. 设计资源 / Design Resources

- Figma 设计稿链接: [待添加]
- 图标库: [Lucide Icons / Heroicons / etc.]
- 插图资源: [链接]

### B. 更新日志 / Changelog

- **v1.0** (2025-11-12): 初始设计规范发布

---

**设计师签名 / Designer Signature:** Claude (Senior UX/UI Designer)
**文档状态 / Document Status:** ✅ 已完成 / Completed
```

---

## 【执行说明】/ Execution Instructions

当用户调用此技能时，按照以下步骤执行：

1. **解析需求**（第一步）
   - 读取 PRD.md（如果存在）
   - 询问用户设计偏好
   - 确认产品约束和目标用户

2. **调研设计趋势**（第二步）
   - 使用 WebSearch 搜索相关设计案例和最佳实践
   - 总结调研结果

3. **制定设计策略**（第三步）
   - 确定设计风格方向
   - 生成色彩系统
   - 定义交互原则和组件清单

4. **生成设计规范文档**（第四步）
   - 创建完整的 DESIGN_SPEC.md 文件
   - 包含所有必需的章节：
     1. 设计原则
     2. 视觉系统（色彩、字体、间距、圆角、阴影、动画）
     3. 交互流程图
     4. 组件标准（shadcn/ui + 自定义组件）
     5. 页面交互文档（至少一个页面的完整交互说明）
     6. 设计稿（桌面端 + 移动端）
   - 确保所有设计决策有明确的理由和使用场景

5. **交付确认**
   - 检查设计交付清单是否完成
   - 询问用户是否需要调整或补充

---

## 【注意事项】/ Important Notes

1. **设计一致性 / Consistency**
   - 所有设计元素必须符合视觉系统定义
   - 不同页面和组件保持风格一致

2. **开发可行性 / Feasibility**
   - 确保设计可以用 React + shadcn/ui + Tailwind CSS 实现
   - 避免过度复杂的交互或动画

3. **无障碍访问 / Accessibility**
   - 色彩对比度符合 WCAG AA 标准
   - 所有交互元素有明确的焦点状态
   - 提供键盘导航支持

4. **性能优化 / Performance**
   - 动画不超过 350ms
   - 避免过度使用阴影和毛玻璃效果
   - 图片和图标优化加载

5. **响应式设计 / Responsive**
   - 所有设计必须适配移动端
   - 定义清晰的断点和适配规则

---

## 【示例调用】/ Example Usage

用户输入：
```
请基于我的 PRD.md 创建设计规范。我希望网站是 Web3 赛博朋克风格，主色调使用霓虹青色 (#00f0ff)，参考 uniswap.org 的设计。
```

助手响应：
```
✅ 已读取 PRD.md，了解产品需求
🎨 设计风格：Web3 赛博朋克
🌈 主色调：霓虹青色 (#00f0ff)
🔍 正在调研 uniswap.org 设计特点和 Web3 设计趋势...

[调研结果总结]

💡 设计策略已制定，正在生成完整的 DESIGN_SPEC.md...

✅ 设计规范文档已生成！包含：
- 完整的视觉系统（色彩、字体、间距等）
- 用户旅程和交互流程图
- shadcn/ui 组件定制规范
- 主页面的详细交互文档
- 桌面端和移动端设计稿

请查看 DESIGN_SPEC.md 文件。如需调整，请告诉我！
```

---

**技能版本 / Skill Version:** v1.0
**最后更新 / Last Updated:** 2025-11-12
**适用技术栈 / Tech Stack:** React 18 + shadcn/ui + Tailwind CSS

---
> Source: [majiayu000/claude-skill-registry](https://github.com/majiayu000/claude-skill-registry) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-07-07 -->
