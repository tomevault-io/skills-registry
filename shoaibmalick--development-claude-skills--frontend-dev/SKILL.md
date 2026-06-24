---
name: frontend-dev
description: Frontend development workflows for React with TypeScript, Tailwind CSS, Context API, and Vitest. Use this skill when creating React components, setting up project structure, implementing state management, styling with Tailwind, or writing tests. Use when this capability is needed.
metadata:
  author: shoaibmalick
---

# Frontend Dev

A comprehensive skill for React + TypeScript frontend development with modern tooling.

## Tech Stack

| Category | Technology |
|----------|------------|
| Framework | React 18+ with TypeScript |
| Styling | Tailwind CSS |
| State Management | Context API |
| Testing | Vitest + React Testing Library |
| Build Tool | Vite |

## Project Structure

```
src/
  components/           # Reusable UI components
    ui/                 # Base UI components (Button, Input, Card)
    features/           # Feature-specific components
  context/              # Context providers and hooks
  hooks/                # Custom React hooks
  pages/                # Page components
  types/                # TypeScript interfaces and types
  utils/                # Utility functions
  __tests__/            # Test files
```

## Component Creation

### Functional Component Template

```tsx
import { FC } from 'react';

interface ComponentNameProps {
  title: string;
  onClick?: () => void;
  children?: React.ReactNode;
}

export const ComponentName: FC<ComponentNameProps> = ({
  title,
  onClick,
  children
}) => {
  return (
    <div className="p-4 rounded-lg bg-white shadow-md">
      <h2 className="text-xl font-semibold text-gray-800">{title}</h2>
      {children}
      {onClick && (
        <button
          onClick={onClick}
          className="mt-4 px-4 py-2 bg-blue-600 text-white rounded hover:bg-blue-700"
        >
          Click Me
        </button>
      )}
    </div>
  );
};
```

## Context API Pattern

```tsx
// context/ThemeContext.tsx
import { createContext, useContext, useState, FC, ReactNode } from 'react';

interface ThemeContextType {
  theme: 'light' | 'dark';
  toggleTheme: () => void;
}

const ThemeContext = createContext<ThemeContextType | undefined>(undefined);

export const ThemeProvider: FC<{ children: ReactNode }> = ({ children }) => {
  const [theme, setTheme] = useState<'light' | 'dark'>('light');
  const toggleTheme = () => setTheme(prev => prev === 'light' ? 'dark' : 'light');

  return (
    <ThemeContext.Provider value={{ theme, toggleTheme }}>
      {children}
    </ThemeContext.Provider>
  );
};

export const useTheme = (): ThemeContextType => {
  const context = useContext(ThemeContext);
  if (!context) throw new Error('useTheme must be used within ThemeProvider');
  return context;
};
```

## Tailwind CSS Common Classes

| Purpose | Classes |
|---------|---------|
| Flexbox Center | `flex items-center justify-center` |
| Grid 3 Columns | `grid grid-cols-3 gap-4` |
| Card Style | `p-4 rounded-lg bg-white shadow-md` |
| Button Primary | `px-4 py-2 bg-blue-600 text-white rounded hover:bg-blue-700` |
| Input Field | `w-full px-3 py-2 border border-gray-300 rounded focus:ring-2` |

## Custom Hooks

### useFetch Hook

```tsx
import { useState, useEffect } from 'react';

export function useFetch<T>(url: string) {
  const [data, setData] = useState<T | null>(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState<string | null>(null);

  useEffect(() => {
    fetch(url)
      .then(res => res.json())
      .then(setData)
      .catch(err => setError(err.message))
      .finally(() => setLoading(false));
  }, [url]);

  return { data, loading, error };
}
```

## Testing with Vitest

```tsx
import { describe, it, expect, vi } from 'vitest';
import { render, screen, fireEvent } from '@testing-library/react';
import { Button } from '../components/ui/Button';

describe('Button', () => {
  it('renders with correct text', () => {
    render(<Button>Click Me</Button>);
    expect(screen.getByText('Click Me')).toBeInTheDocument();
  });

  it('calls onClick when clicked', () => {
    const handleClick = vi.fn();
    render(<Button onClick={handleClick}>Click Me</Button>);
    fireEvent.click(screen.getByText('Click Me'));
    expect(handleClick).toHaveBeenCalledTimes(1);
  });
});
```

## Common Commands

```bash
npm run dev              # Start dev server
npm run build            # Build for production
npm run test             # Run tests
npm run test:watch       # Run tests in watch mode
npm run lint             # Run ESLint
```

## References

For detailed guides, see:
- references/react-patterns.md - Advanced React patterns
- references/tailwind-guide.md - Tailwind cheatsheet
- references/testing-guide.md - Testing best practices

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/shoaibmalick) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
