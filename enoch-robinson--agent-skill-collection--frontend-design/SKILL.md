---
name: frontend-design
description: 前端界面设计技能。创建独特、高质量的前端界面，避免通用 AI 风格。当用户需要构建网站、落地页、仪表盘、React 组件、HTML/CSS 布局或美化任何 Web UI 时使用此技能。 Use when this capability is needed.
metadata:
  author: enoch-robinson
---

# Frontend Design

创建独特、生产级的前端界面，避免"AI 风格"的通用设计。

## 设计思维

在编码前，确定大胆的美学方向：

1. **目的**：界面解决什么问题？谁在使用？
2. **风格**：选择一个极端方向- 极简主义 / 极繁主义
   - 复古未来 / 有机自然
   - 奢华精致 / 玩具趣味
   - 杂志编辑 / 粗野主义
   - 工业实用 / 柔和粉彩
3. **差异化**：什么让它令人难忘？

## 美学指南

### 字体
- **避免**：Arial、Inter、Roboto、系统字体
- **选择**：独特、有个性的字体
- **搭配**：展示字体 + 正文字体

### 配色
- 使用 CSS 变量保持一致
- 主色 + 强调色优于平均分布
- 避免：紫色渐变白底（AI 风格）

### 动效
- 优先 CSS 动画
- 重点时刻：页面加载、滚动触发、悬停状态
- 使用 `animation-delay` 创建错落效果

### 布局
- 打破常规：不对称、重叠、对角线
- 大胆留白或控制密度
- 网格突破元素

### 背景与细节
- 渐变网格、噪点纹理、几何图案
- 层叠透明、戏剧阴影
- 自定义光标、装饰边框

## 禁止事项

❌ 通用字体（Inter、Roboto、Arial）
❌ 紫色渐变白底
❌ 可预测的布局和组件
❌ 缺乏上下文特色的设计

## 代码示例

```html
<!-- 独特的按钮设计 -->
<button class="btn-brutalist">
  点击我
</button>

<style>
.btn-brutalist {
  font-family: 'Space Mono', monospace;
  background: #000;
  color: #fff;
  border: 3px solid #000;
  padding: 1rem 2rem;
  font-weight: bold;
  text-transform: uppercase;
  box-shadow: 4px 4px 0 #ff3366;
  transition: all 0.1s ease;
}

.btn-brutalist:hover {
  transform: translate(-2px, -2px);
  box-shadow: 6px 6px 0 #ff3366;
}
</style>
```

## 原则

- **大胆选择**：极简或极繁都可以，关键是意图明确
- **执行到位**：匹配复杂度与美学愿景
- **独特性**：每个设计都应不同，避免趋同
- **创造力**：Claude 能创造非凡作品，不要保守

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/enoch-robinson) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
