---
name: web-accessibility
description: 遵循 WCAG 指南构建无障碍 Web 应用。适用于实现 ARIA 模式、键盘导航、屏幕阅读器支持或确保无障碍合规性。触发关键词：accessibility, a11y, WCAG, ARIA, screen reader, keyboard navigation, 无障碍, 可访问性。 Use when this capability is needed.
metadata:
  author: 1837620622
---

# Web 无障碍 (a11y)

遵循 WCAG 2.1 指南构建无障碍 Web 应用。

## ARIA 模式

### 按钮
```tsx
<button
  type="button"
  aria-pressed={isPressed}
  aria-disabled={isDisabled}
  onClick={handleClick}
>
  Toggle Feature
</button>
```

### 模态对话框
```tsx
<div
  role="dialog"
  aria-modal="true"
  aria-labelledby="modal-title"
  aria-describedby="modal-description"
>
  <h2 id="modal-title">Confirm Action</h2>
  <p id="modal-description">Are you sure you want to proceed?</p>
  <button onClick={onConfirm}>Confirm</button>
  <button onClick={onCancel}>Cancel</button>
</div>
```

### 导航菜单
```tsx
<nav aria-label="Main navigation">
  <ul role="menubar">
    <li role="none">
      <a role="menuitem" href="/home">Home</a>
    </li>
    <li role="none">
      <button
        role="menuitem"
        aria-haspopup="true"
        aria-expanded={isOpen}
      >
        Products
      </button>
      {isOpen && (
        <ul role="menu" aria-label="Products submenu">
          <li role="none">
            <a role="menuitem" href="/products/new">New</a>
          </li>
        </ul>
      )}
    </li>
  </ul>
</nav>
```

## 键盘导航

### 焦点管理
```tsx
import { useEffect, useRef } from 'react';

function Modal({ isOpen, onClose, children }) {
  const modalRef = useRef<HTMLDivElement>(null);
  const previousFocus = useRef<HTMLElement | null>(null);

  useEffect(() => {
    if (isOpen) {
      previousFocus.current = document.activeElement as HTMLElement;
      modalRef.current?.focus();
    } else {
      previousFocus.current?.focus();
    }
  }, [isOpen]);

  // 在模态框内困住焦点
  const handleKeyDown = (e: React.KeyboardEvent) => {
    if (e.key === 'Escape') {
      onClose();
    }
    
    if (e.key === 'Tab') {
      const focusable = modalRef.current?.querySelectorAll(
        'button, [href], input, select, textarea, [tabindex]:not([tabindex="-1"])'
      );
      
      if (focusable && focusable.length > 0) {
        const first = focusable[0] as HTMLElement;
        const last = focusable[focusable.length - 1] as HTMLElement;
        
        if (e.shiftKey && document.activeElement === first) {
          e.preventDefault();
          last.focus();
        } else if (!e.shiftKey && document.activeElement === last) {
          e.preventDefault();
          first.focus();
        }
      }
    }
  };

  if (!isOpen) return null;

  return (
    <div
      ref={modalRef}
      role="dialog"
      aria-modal="true"
      tabIndex={-1}
      onKeyDown={handleKeyDown}
    >
      {children}
    </div>
  );
}
```

## 颜色对比度

最小对比度要求 (WCAG AA):
- 普通文本: 4.5:1
- 大文本 (18pt+): 3:1
- UI 组件: 3:1

```typescript
function getContrastRatio(color1: string, color2: string): number {
  const lum1 = getLuminance(color1);
  const lum2 = getLuminance(color2);
  const lighter = Math.max(lum1, lum2);
  const darker = Math.min(lum1, lum2);
  return (lighter + 0.05) / (darker + 0.05);
}

function getLuminance(hex: string): number {
  const rgb = hexToRgb(hex);
  const [r, g, b] = rgb.map((c) => {
    c = c / 255;
    return c <= 0.03928 ? c / 12.92 : Math.pow((c + 0.055) / 1.055, 2.4);
  });
  return 0.2126 * r + 0.7152 * g + 0.0722 * b;
}
```

## 无障碍表单

```tsx
<form onSubmit={handleSubmit}>
  <div>
    <label htmlFor="email">
      Email address
      <span aria-hidden="true">*</span>
      <span className="sr-only">(required)</span>
    </label>
    <input
      id="email"
      type="email"
      aria-required="true"
      aria-invalid={errors.email ? 'true' : 'false'}
      aria-describedby={errors.email ? 'email-error' : undefined}
    />
    {errors.email && (
      <p id="email-error" role="alert" className="error">
        {errors.email}
      </p>
    )}
  </div>
  
  <button type="submit">Submit</button>
</form>
```

## 仅屏幕阅读器可见内容

```css
.sr-only {
  position: absolute;
  width: 1px;
  height: 1px;
  padding: 0;
  margin: -1px;
  overflow: hidden;
  clip: rect(0, 0, 0, 0);
  white-space: nowrap;
  border: 0;
}
```

## 测试

```bash
# 自动化测试
npm install -D axe-core @axe-core/react

# 在测试中
import { axe, toHaveNoViolations } from 'jest-axe';
expect.extend(toHaveNoViolations);

test('component is accessible', async () => {
  const { container } = render(<MyComponent />);
  const results = await axe(container);
  expect(results).toHaveNoViolations();
});
```

## 相关资源

- **WCAG 2.1 指南**: https://www.w3.org/WAI/WCAG21/quickref/
- **ARIA 编写实践**: https://www.w3.org/WAI/ARIA/apg/

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/1837620622) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
