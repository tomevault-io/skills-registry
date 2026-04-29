---
name: react-component-architecture
description: > Use when this capability is needed.
metadata:
  author: aj-geddes
---

# React Component Architecture

## Table of Contents

- [Overview](#overview)
- [When to Use](#when-to-use)
- [Quick Start](#quick-start)
- [Reference Guides](#reference-guides)
- [Best Practices](#best-practices)

## Overview

Build scalable, maintainable React components using modern patterns including functional components, hooks, composition, and TypeScript for type safety.

## When to Use

- Component library design
- Large-scale React applications
- Reusable UI patterns
- Custom hooks development
- Performance optimization

## Quick Start

Minimal working example:

```typescript
// Button.tsx
import React, { useState, useCallback } from 'react';

interface ButtonProps {
  variant?: 'primary' | 'secondary' | 'danger';
  size?: 'sm' | 'md' | 'lg';
  disabled?: boolean;
  onClick?: () => void;
  children: React.ReactNode;
}

export const Button: React.FC<ButtonProps> = ({
  variant = 'primary',
  size = 'md',
  disabled = false,
  onClick,
  children
}) => {
  const variantStyles = {
    primary: 'bg-blue-500 hover:bg-blue-600',
    secondary: 'bg-gray-500 hover:bg-gray-600',
    danger: 'bg-red-500 hover:bg-red-600'
  };

  const sizeStyles = {
// ... (see reference guides for full implementation)
```

## Reference Guides

Detailed implementations in the `references/` directory:

| Guide | Contents |
|---|---|
| [Functional Component with Hooks](references/functional-component-with-hooks.md) | Functional Component with Hooks |
| [Custom Hooks Pattern](references/custom-hooks-pattern.md) | Custom Hooks Pattern |
| [Composition Pattern](references/composition-pattern.md) | Composition Pattern |
| [Higher-Order Component (HOC)](references/higher-order-component-hoc.md) | Higher-Order Component (HOC) |
| [Render Props Pattern](references/render-props-pattern.md) | Render Props Pattern |

## Best Practices

### ✅ DO

- Follow established patterns and conventions
- Write clean, maintainable code
- Add appropriate documentation
- Test thoroughly before deploying

### ❌ DON'T

- Skip testing or validation
- Ignore error handling
- Hard-code configuration values

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/aj-geddes) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
