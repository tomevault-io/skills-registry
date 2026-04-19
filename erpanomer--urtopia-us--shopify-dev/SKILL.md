---
name: shopify-tailwind-development-urtopia-us
description: 针对 Urtopia US 项目的深度开发指南 (Antigravity/Trae/Cursor)。包含 Tailwind 前缀、Web Components 架构及构建流程。 Use when this capability is needed.
metadata:
  author: erpanomer
---

# Shopify Tailwind Development Skill (Urtopia US)

## 0. 项目概览
此项目是一个高度定制的 Shopify 2.0 主题。
- **构建工具**: Tailwind CSS (JIT mode)
- **核心前缀**: `er-` (例如 `er-container`, `er-block`)
- **JS 架构**: Vanilla JS Web Components

## 1. 核心开发规则

### CSS (Tailwind)
*   **源文件**: `tailwind.css` (位于根目录)
*   **输出文件**: `assets/tailwind.min.css` (自动生成，勿改)
*   **命令**: `npm run dev` (同时启动 Shopify Theme Watch 和 Tailwind Watch)
*   **重要**: 如果你添加了新的 Tailwind 类但没生效，检查是否忘记加 `er-` 前缀，或者 `content` 配置未覆盖新文件路径。

### JavaScript (Web Components)
项目广泛使用自定义元素。示例模板：

```javascript
class MyCustomElement extends HTMLElement {
  constructor() {
    super();
    this.init();
  }

  init() {
    this.addEventListener('click', this.handleClick.bind(this));
  }

  handleClick() {
    console.log('Clicked!');
  }
}

customElements.define('my-custom-element', MyCustomElement);
```

**可用工具函数 (`assets/global.js`)**:
- `trapFocus(element)`: 用于弹窗打开时锁定焦点。
- `removeTrapFocus(elementToFocus)`: 弹窗关闭时还原焦点。
- `debounce(fn, wait)`: 性能优化。

### Liquid & Schema
*   **Image Picker**: 始终支持 `image_picker` 并配合 `image_url` 过滤器。
*   **Looping**: 使用 `forloop.index` 生成唯一 ID。

## 2. 常见任务指南

### 任务：创建新 Section
1.  在 `sections/` 下创建 `.liquid` 文件。
2.  编写 HTML 结构，使用 `er-` 类名。
3.  在文件底部定义 `{% schema %}`。
4.  如果需要交互，定义一个 Web Component 并在 Schema 的 `presets` 中由该 Section 引入（或全局引入）。

### 任务：修改 Header
*   不要直接修改 `snippets/header-dropdown.liquid` 除非确定逻辑。
*   主入口是 `sections/header.liquid`。
*   注意 `sticky-header` 自定义元素及其逻辑。

## 3. 调试与验证
*   **样式问题**: 检查 Chrome DevTools 中元素的 class 是否有 `er-`。
*   **JS 报错**: 检查 `customElements.define` 命名是否包含连字符（kebab-case）。

## 4. 给 AI 的提示 (Trae/Cursor)
当你作为 AI 助手编写代码时：
1.  **自我纠正**: 写完 `class="flex"` 后立即改为 `class="er-flex"`。
2.  **上下文意识到**: 看到 `pf-xxx.liquid` 文件时，提示用户这是 PageFly 文件，询问是否确定要修改。

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/erpanomer) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
