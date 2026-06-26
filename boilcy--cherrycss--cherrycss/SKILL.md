---
name: cherrycss-theme
description: Cherry Studio 主题 CSS 制作指南 —— 了解 CSS 变量系统、毛玻璃技法、渐变背景等，最终输出可直接粘贴使用的 CSS 代码。 Use when this capability is needed.
metadata:
  author: boilcy
---

# CherryCSS 主题 CSS 制作指南

本 skill 指导你为 Cherry Studio 制作自定义 CSS 主题，**最终输出是一段可直接粘贴到 Cherry Studio 设置 → 显示设置 → 自定义 CSS 的代码**。

制作主题前，可先访问 CherryCSS 项目网站 [cherrycss.com](https://cherrycss.com) 浏览已有主题的样式和效果，获取配色和风格灵感喵~

## Cherry Studio CSS 变量系统

Cherry Studio 通过 CSS 变量控制主题外观。核心变量如下：

### 颜色变量

| 变量 | 作用 | 暗色模式参考值 | 亮色模式参考值 |
|------|------|---------------|---------------|
| `--color-background` | 主背景 | 深色 | 亮色 |
| `--color-background-soft` | 次背景 | 稍亮 | 稍暗 |
| `--color-background-mute` | 更深层背景 | 更亮/更暗 | 更暗/更亮 |
| `--color-primary` | 主题强调色 | #7C5CFC | #7C5CFC |
| `--color-primary-soft` | 柔和主题色 | primary 60% 透明度 | 同左 |
| `--color-primary-mute` | 淡主题色 | primary 20% 透明度 | 同左 |

### 文字变量

| 变量 | 作用 |
|------|------|
| `--color-text-1` | 主文字 |
| `--color-text-2` | 次文字（70% 透明度） |
| `--color-text-3` | 占位文字（45% 透明度） |

### 界面变量

| 变量 | 作用 |
|------|------|
| `--color-border` | 边框色 |
| `--chat-background-user` | 用户消息背景 |
| `--chat-background-assistant` | AI 消息背景 |
| `--navbar-background` / `--navbar-background-mac` | 导航栏背景 |
| `--color-hover` | 悬停态背景 |
| `--color-active` | 激活态背景 |
| `--color-code-background` | 代码块背景 |

## 常用 CSS 技法

### 1. 基础变量覆盖

两种模式都需要覆盖，格式如下：

```css
/* 暗色模式 */
body[theme-mode="dark"] {
  --color-background: #1A1A2E;
  --color-text-1: #F0EEF5;
  --chat-background-user: rgba(124, 92, 252, 0.15);
  /* ... */
}

/* 亮色模式 */
body[theme-mode="light"] {
  --color-background: #F0EEF5;
  --color-text-1: #2D2B3A;
  --chat-background-user: rgba(124, 92, 252, 0.08);
  /* ... */
}
```

### 2. 毛玻璃效果（核心属性：backdrop-filter）

```css
.glass {
  background: rgba(20, 16, 50, 0.50) !important;
  backdrop-filter: blur(16px) saturate(180%) !important;
  -webkit-backdrop-filter: blur(16px) saturate(180%) !important;
}
```

建议分层模糊强度：
- `8px` — 消息气泡、代码块
- `12px` — 内容容器、折叠面板
- `16px` — 侧边栏、导航栏、输入框
- `20px` — 模态框、下拉菜单

### 3. 渐变背景

```css
body[theme-mode="dark"] {
  --color-background: linear-gradient(135deg,
    rgba(15, 12, 41, 0.92) 0%,
    rgba(48, 43, 99, 0.85) 50%,
    rgba(36, 36, 62, 0.92) 100%
  );
}
```

配合 `body::after` 伪元素实现毛玻璃渐变背景：

```css
body::after {
  content: "";
  position: fixed;
  inset: 0;
  z-index: -1;
  background: var(--color-background);
  backdrop-filter: blur(16px) saturate(180%);
}
```

### 4. 消息气泡边框

```css
.chat-item.user .message-content-container {
  border-left: 3px solid rgba(124, 92, 252, 0.40) !important;
}

.chat-item.assistant .message-content-container {
  border-left: 3px solid rgba(255, 255, 255, 0.10) !important;
}
```

### 5. 输入框聚焦光晕

```css
#inputbar:focus-within {
  border-color: rgba(124, 92, 252, 0.50) !important;
  box-shadow: 0 0 12px rgba(124, 92, 252, 0.15) !important;
}
```

## 输出格式

根据用户需求输出完整可用的一段 CSS，包含：
1. 注释头（主题名称、风格说明）
2. CSS 变量覆盖（深色 + 亮色）
3. 特定元素样式定制

示例结构：

```css
/*
========================
主题名称 · 风格说明
========================
支持 Windows / macOS 双平台
*/

/* ===== 全局变量 ===== */
:root {
  /* 全局变量定义 */
}

/* ===== 暗色模式 ===== */
body[theme-mode="dark"] {
  /* 暗色变量覆盖 */
}

/* ===== 亮色模式 ===== */
body[theme-mode="light"] {
  /* 亮色变量覆盖 */
}

/* ===== 特定元素样式 ===== */
/* 侧边栏、导航栏、输入框、消息气泡、面板等 */
```

---
> Source: [boilcy/CherryCSS](https://github.com/boilcy/CherryCSS) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-24 -->
