---
name: product-design-skill
description: Product design and UX/UI guidance for real_deal platform. Use when designing new features, improving user interactions, creating UI components, or optimizing user experience. Covers product positioning, design principles, interaction patterns, visual design, accessibility, and responsive design. Use when this capability is needed.
metadata:
  author: phuhao00
---

# Product Design (产品体验设计)

## Product Positioning (产品定位)

### Target Audience (目标用户)

1. **初创公司创始人**
   - 需要展示项目和产品
   - 寻找投资人和人才
   - 建立品牌影响力

2. **投资人**
   - 发现优质初创公司
   - 查看 Pitch 页面和 Deal Room
   - 高效筛选和评估项目

3. **求职者**
   - 查看职位信息
   - 了解公司背景
   - 直接联系招聘方

4. **企业**
   - 发布职位信息
   - 展示公司实力
   - 招聘优秀人才

### Core Values (核心价值)

- **简洁高效**: 清晰的信息层级，快速找到所需内容
- **专业可信**: 设计体现专业性和可靠性
- **智能匹配**: AI 驱动的个性化推荐
- **开放连接**: 促进人脉和机会的连接

## Design Principles (设计原则)

### 1. Clarity First (清晰优先)
- 信息层次分明，重要内容突出
- 使用恰当的留白和间距
- 文字清晰易读，字号合理
- 避免视觉噪音和不必要的装饰

### 2. Efficiency (高效)
- 减少操作步骤，常用功能触手可及
- 智能预填充和自动完成
- 快捷键和手势支持
- 加载状态和反馈及时

### 3. Consistency (一致性)
- 组件和交互模式统一
- 视觉风格一致
- 术语使用统一
- 跨平台体验一致

### 4. Accessibility (可访问性)
- 键盘导航支持
- 屏幕阅读器兼容
- 色彩对比度符合 WCAG 标准
- 响应式设计适配各种设备

### 5. Feedback (及时反馈)
- 操作结果即时展示
- 加载状态明确
- 错误信息清晰
- 成功提示明显

## Visual Design (视觉设计)

### Color System (色彩系统)

#### Primary Colors (主色)
```
--primary: 0 100 220          // 主蓝色
--primary-hover: 0 100 200    // 悬停态
--primary-active: 0 100 180   // 激活态
```

#### Semantic Colors (语义色)
```
--success: 140 180 80         // 成功绿
--warning: 45 180 180         // 警告黄
--danger: 0 180 180           // 危险红
--info: 200 100 220           // 信息蓝
```

#### Neutral Colors (中性色)
```
--bg: 255 255 255             // 背景
--fg: 0 0 0                   // 前景
--border: 240 240 240         // 边框
--subtle: 140 140 140         // 辅助文本
--muted: 180 180 180          // 弱化文本
```

### Typography (排版)

#### Font Scale (字号系统)
```
--font-xs: 12px              // 辅助信息
--font-sm: 14px              // 次要文本
--font-base: 15px            // 正文
--font-md: 16px              // 标题小
--font-lg: 18px              // 标题中
--font-xl: 24px              // 标题大
--font-2xl: 32px             // 页面标题
```

#### Font Weights (字重)
```
--font-normal: 400
--font-medium: 500
--font-semibold: 600
--font-bold: 700
```

#### Line Heights (行高)
```
--leading-tight: 1.25
--leading-normal: 1.5
--leading-relaxed: 1.75
```

### Spacing (间距)

#### Scale (间距系统)
```
--space-0: 0
--space-1: 4px
--space-2: 8px
--space-3: 12px
--space-4: 16px
--space-6: 24px
--space-8: 32px
--space-12: 48px
--space-16: 64px
```

### Shadows (阴影)

```
--shadow-sm: 0 1px 2px rgba(0,0,0,0.05)
--shadow-md: 0 4px 6px rgba(0,0,0,0.07)
--shadow-lg: 0 10px 15px rgba(0,0,0,0.1)
--shadow-xl: 0 20px 25px rgba(0,0,0,0.15)
```

### Border Radius (圆角)

```
--radius-sm: 4px             // 按钮、标签
--radius-md: 8px             // 卡片、输入框
--radius-lg: 12px            // 面板
--radius-xl: 16px            // 大卡片
--radius-full: 9999px        // 圆形、胶囊按钮
```

## Interaction Design (交互设计)

### Button Patterns (按钮模式)

#### Primary Button (主按钮)
- 用于主要操作
- 显眼的视觉样式
- 悬停和激活状态

```css
.btn-primary {
  background: rgb(var(--primary));
  color: white;
  padding: 8px 16px;
  border-radius: var(--radius-full);
  font-weight: var(--font-medium);
  transition: all 0.2s;
}

.btn-primary:hover {
  background: rgb(var(--primary-hover));
  transform: translateY(-1px);
}

.btn-primary:active {
  background: rgb(var(--primary-active));
  transform: translateY(0);
}

.btn-primary:disabled {
  opacity: 0.5;
  cursor: not-allowed;
}
```

#### Secondary Button (次按钮)
- 用于次要操作
- 较低视觉优先级

```css
.btn-secondary {
  background: transparent;
  color: rgb(var(--fg));
  border: 1px solid rgb(var(--border));
  padding: 8px 16px;
  border-radius: var(--radius-full);
  font-weight: var(--font-medium);
}

.btn-secondary:hover {
  background: rgb(var(--subtle) / 0.1);
}
```

#### Ghost Button (透明按钮)
- 用于低优先级操作
- 保持视觉简洁

```css
.btn-ghost {
  background: transparent;
  color: rgb(var(--subtle));
  padding: 8px 12px;
  border-radius: var(--radius-md);
}

.btn-ghost:hover {
  background: rgb(var(--subtle) / 0.1);
  color: rgb(var(--fg));
}
```

### Input Patterns (输入框模式)

#### Text Input (文本输入)
```css
.input {
  width: 100%;
  padding: 8px 12px;
  border: 1px solid rgb(var(--border));
  border-radius: var(--radius-md);
  font-size: var(--font-base);
  transition: border-color 0.2s;
}

.input:focus {
  outline: none;
  border-color: rgb(var(--primary));
  box-shadow: 0 0 0 3px rgb(var(--primary) / 0.1);
}

.input::placeholder {
  color: rgb(var(--muted));
}
```

#### Search Input (搜索输入)
- 搜索图标
- 清除按钮
- 搜索建议

### Card Patterns (卡片模式)

#### Base Card (基础卡片)
```css
.card {
  background: rgb(var(--bg));
  border: 1px solid rgb(var(--border));
  border-radius: var(--radius-lg);
  padding: var(--space-4);
  transition: box-shadow 0.2s;
}

.card:hover {
  box-shadow: var(--shadow-md);
}
```

#### Interactive Card (可交互卡片)
- 点击反馈
- 悬停效果
- 焦点状态

```css
.card-interactive {
  cursor: pointer;
  transition: all 0.2s;
}

.card-interactive:hover {
  transform: translateY(-2px);
  box-shadow: var(--shadow-lg);
}

.card-interactive:active {
  transform: translateY(0);
}
```

### Navigation Patterns (导航模式)

#### Main Navigation (主导航)
- 固定顶部或左侧
- 清晰的当前状态
- 徽章通知

#### Tab Navigation (标签导航)
- 水平排列
- 下划线指示
- 平滑切换

#### Breadcrumb (面包屑)
- 层级路径
- 点击跳转
- 最后项不可点击

## Component Design (组件设计)

### Avatar (头像)
```
- 圆形或圆角矩形
- 显示首字母或图片
- 大小：xs(24px)、sm(32px)、md(40px)、lg(48px)、xl(64px)
- 在线状态指示器
```

### Badge (徽章)
```
- 小型状态标签
- 圆角矩形或胶囊形
- 语义颜色（成功、警告、危险、信息）
- 带图标可选
```

### Chip (标签)
```
- 交互式标签
- 可关闭
- 多选状态
- 过滤功能
```

### Modal (弹窗)
```
- 遮罩背景
- 居中或侧边弹出
- 关闭按钮（X 和 ESC）
- 动画过渡
- 焦点陷阱
```

### Dropdown (下拉菜单)
```
- 触发器按钮/链接
- 悬停或点击触发
- 分隔线分组
- 快捷键提示
```

### Tooltip (提示框)
```
- 悬停触发
- 延迟显示（300ms）
- 四个位置（上/下/左/右）
- 自动调整避免超出视口
```

### Skeleton (骨架屏)
```
- 加载占位
- 微光动画
- 匹配实际内容布局
```

### Empty State (空状态)
```
- 友好的插图或图标
- 清晰的说明文字
- 引导操作按钮
```

## Layout Patterns (布局模式)

### Feed Layout (信息流)
```
三栏布局：
- 左：导航菜单（240px 固定）
- 中：内容滚动（600-700px）
- 右：侧边栏（300px 固定）
```

### Profile Layout (个人资料)
```
- 头部：大封面 + 头像
- 信息：标签页导航
- 内容：动态/项目/产品
```

### Detail Page (详情页)
```
- 面包屑导航
- 标题区：标题 + 操作按钮
- 内容区：多列布局
- 侧边栏：相关信息
```

### Form Layout (表单布局)
```
- 单列：简单表单
- 双列：复杂表单
- 分步：长表单
- 垂直或水平对齐
```

## Responsive Design (响应式设计)

### Breakpoints (断点)
```
--breakpoint-sm: 640px   // 手机横屏
--breakpoint-md: 768px   // 平板
--breakpoint-lg: 1024px  // 小笔记本
--breakpoint-xl: 1280px  // 大笔记本
--breakpoint-2xl: 1536px // 桌面
```

### Mobile First (移动优先)
```
1. 默认样式（手机）
2. 小屏幕（sm: 640px+）
3. 中等屏幕（md: 768px+）
4. 大屏幕（lg: 1024px+）
5. 超大屏幕（xl: 1280px+）
```

### Adaptive Patterns (自适应模式)

```
Mobile (< 768px):
- 单列布局
- 汉堡菜单
- 全宽卡片
- 底部导航栏

Tablet (768px - 1024px):
- 双列布局
- 侧边导航
- 优化的卡片尺寸

Desktop (1024px+):
- 三栏布局
- 完整导航
- 密集信息展示
```

## Accessibility (可访问性)

### Color Contrast (色彩对比度)
- 文本 AAA: 7:1
- 文本 AA: 4.5:1
- 大文本 AA: 3:1
- 图标和图形: 3:1

### Keyboard Navigation (键盘导航)
- Tab 顺序符合逻辑
- Focus 状态可见
- Esc 关闭弹窗
- 方向键导航列表

### Screen Reader (屏幕阅读器)
- 语义化 HTML
- ARIA 标签
- Alt 文本描述
- 表单标签关联

### Touch Targets (触摸目标)
- 最小尺寸：44×44px
- 间距至少 8px
- 避免误触

## Animation (动画)

### Principles (原则)
```
1. 快速且流畅（200-300ms）
2. 自然的缓动函数
3. 不干扰用户操作
4. 保持一致性
```

### Transitions (过渡)
```
--ease-default: cubic-bezier(0.4, 0, 0.2, 1)
--ease-in: cubic-bezier(0.4, 0, 1, 1)
--ease-out: cubic-bezier(0, 0, 0.2, 1)
--ease-in-out: cubic-bezier(0.4, 0, 0.2, 1)
```

### Common Animations (常用动画)
```
- Fade in/out: 淡入淡出
- Slide up/down: 上下滑动
- Scale: 缩放
- Rotate: 旋转
- Shimmer: 微光效果
```

## Content Design (内容设计)

### Copywriting (文案)
```
1. 简洁明了
2. 主动语态
3. 用户视角
4. 一致性
```

### Tone of Voice (语调)
```
- 专业但不严肃
- 友好但不随意
- 清晰但不刻板
- 有帮助但不推销
```

### Error Messages (错误信息)
```
- 清晰说明问题
- 提供解决方案
- 使用友好语气
- 避免技术术语
```

## Design Process (设计流程)

### 1. Research (研究)
- 用户调研
- 竞品分析
- 数据分析
- 场景分析

### 2. Ideate (构思)
- 头脑风暴
- 用户旅程
- 信息架构
- 原型草图

### 3. Design (设计)
- 高保真设计
- 交互原型
- 设计规范
- 组件库

### 4. Test (测试)
- 可用性测试
- A/B 测试
- 用户反馈
- 迭代优化

### 5. Implement (实现)
- 设计交付
- 开发协作
- 质量把控
- 上线验证

## Design Deliverables (设计交付)

### Visual Assets (视觉资源)
- 设计稿
- 切图
- 图标
- 插画

### Documentation (文档)
- 设计规范
- 组件文档
- 交互说明
- 交付说明

### Handoff (交付)
- 标注尺寸
- 导出资源
- 设计系统链接
- 开发需求文档

## Common UX Patterns (常用 UX 模式)

### Onboarding (引导)
- 欢迎页
- 功能介绍
- 快速上手
- 渐进式披露

### Empty States (空状态)
- 友好提示
- 引导操作
- 视觉优化

### Loading States (加载状态)
- 骨架屏
- 进度条
- 加载动画
- 分页加载

### Error Handling (错误处理)
- 友好提示
- 重试机制
- 替代方案
- 客服引导

### Confirmation (确认)
- 成功提示
- 撤销操作
- 后续步骤
- 反馈收集

## Performance Considerations (性能考虑)

### Loading Performance (加载性能)
- 图片优化
- 代码分割
- 懒加载
- 缓存策略

### Interaction Performance (交互性能)
- 60fps 动画
- 防抖节流
- 虚拟列表
- 预加载

### Accessibility Performance (可访问性性能)
- ARIA 延迟加载
- 焦点管理
- 键盘导航
- 屏幕阅读器优化

## Design Tools (设计工具)

### Figma
- UI/UX 设计
- 原型制作
- 设计系统
- 团队协作

### Framer
- 交互原型
- 动画设计
- 组件交互

### Principle
- 高保真交互
- 复杂动画
- 微交互

### Sketch
- 矢量设计
- 插件生态
- 设计系统

## Resources (资源)

### Design Inspiration
- Dribbble
- Behance
- Awwwards
- Mobbin

### Design Systems
- Material Design
- Human Interface Guidelines
- Ant Design
- Shadcn/ui

### Accessibility Tools
- axe DevTools
- WAVE
- Lighthouse
- Colour Contrast Checker

## Best Practices (最佳实践)

### Before Starting Design
1. 了解用户和场景
2. 明确设计目标
3. 研究现有设计
4. 制定设计策略

### During Design
1. 保持一致性
2. 遵循设计规范
3. 考虑边界情况
4. 注重可访问性

### After Design
1. 用户测试
2. 收集反馈
3. 迭代优化
4. 文档更新

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/phuhao00) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
