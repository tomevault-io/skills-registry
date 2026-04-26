---
name: frontend-state-react-context
description: Standardized guidelines and patterns for Frontend State React Context. Use when this capability is needed.
metadata:
  author: valec3
---

# Frontend State React Context

## When to use this skill
- Avoiding prop drilling
- Sharing theme/locale
- Authentication state
- Small global state needs

## Workflow
- [ ] Create context with `createContext`
- [ ] Create provider component
- [ ] Create custom hook for consumption
- [ ] Split contexts to avoid re-renders
- [ ] Memoize provider value

## Instructions

### Basic Pattern
```typescript
import { createContext, useContext, useState } from 'react';

interface ThemeContextType {
  theme: 'light' | 'dark';
  toggleTheme: () => void;
}

const ThemeContext = createContext<ThemeContextType | null>(null);

export function ThemeProvider({ children }: { children: React.ReactNode }) {
  const [theme, setTheme] = useState<'light' | 'dark'>('light');

  const value = useMemo(() => ({
    theme,
    toggleTheme: () => setTheme(t => t === 'light' ? 'dark' : 'light')
  }), [theme]);

  return (
    <ThemeContext.Provider value={value}>
      {children}
    </ThemeContext.Provider>
  );
}

export function useTheme() {
  const context = useContext(ThemeContext);
  if (!context) throw new Error('useTheme must be used within ThemeProvider');
  return context;
}
```

## Resources
- Memoize provider value
- Split contexts by concern
- Not ideal for frequently changing state

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/valec3) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
