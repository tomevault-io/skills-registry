---
name: inkjs-design
description: | Use when this capability is needed.
metadata:
  author: akiojin
---

# Ink.js Design

Comprehensive guide for building terminal UIs with Ink.js (React for CLI).

## Quick Start

### Creating a New Component

1. Determine component type: Screen / Part / Common
2. Reference [component-patterns.md](references/component-patterns.md) for similar patterns
3. Add type definitions
4. Implement component
5. Write tests

### Common Issues & Solutions

| Issue | Reference |
|-------|-----------|
| Emoji width misalignment | [ink-gotchas.md](references/ink-gotchas.md#icon-emoji-width) |
| Ctrl+C called twice | [ink-gotchas.md](references/ink-gotchas.md#ctrl-c-handling) |
| useInput conflicts | [ink-gotchas.md](references/ink-gotchas.md#useinput-conflicts) |
| Layout breaking | [responsive-layout.md](references/responsive-layout.md) |
| Screen navigation | [multi-screen-navigation.md](references/multi-screen-navigation.md) |

## Directory Conventions

```
src/cli/ui/
├── components/
│   ├── App.tsx              # Root component with screen management
│   ├── common/              # Common input components (Select, Input)
│   ├── parts/               # Reusable UI parts (Header, Footer)
│   └── screens/             # Full-screen components
├── hooks/                   # Custom hooks
├── utils/                   # Utility functions
└── types.ts                 # Type definitions
```

## Component Classification

### Screen (Full-page views)

- Represents a complete screen/page
- Handles keyboard input via `useInput`
- Implements Header/Content/Footer layout
- Manages screen-level state

### Part (Reusable elements)

- Reusable UI building blocks
- Optimized with `React.memo`
- Stateless/pure components preferred
- Accept configuration via props

### Common (Input components)

- Basic input components
- Support both controlled and uncontrolled modes
- Handle focus management
- Provide consistent UX

## Essential Patterns

### 1. Icon Width Override

Fix string-width v8 emoji width calculation issues:

```typescript
const WIDTH_OVERRIDES: Record<string, number> = {
  "⚡": 1, "✨": 1, "🐛": 1, "🔥": 1, "🚀": 1,
  "🟢": 1, "🟠": 1, "✅": 1, "⚠️": 1,
};

const getIconWidth = (icon: string): number => {
  const baseWidth = stringWidth(icon);
  const override = WIDTH_OVERRIDES[icon];
  return override !== undefined ? Math.max(baseWidth, override) : baseWidth;
};
```

### 2. useInput Conflict Avoidance

Multiple useInput hooks all fire - use early return or isActive:

```typescript
useInput((input, key) => {
  if (disabled) return;  // Early return when inactive
  // Handle input...
}, { isActive: isFocused });
```

### 3. Ctrl+C Handling

```typescript
render(<App />, { exitOnCtrlC: false });

// In component
const { exit } = useApp();
useInput((input, key) => {
  if (key.ctrl && input === "c") {
    cleanup();
    exit();
  }
});
```

### 4. Dynamic Height Calculation

```typescript
const { rows } = useTerminalSize();
const HEADER_LINES = 3;
const FOOTER_LINES = 2;
const contentHeight = rows - HEADER_LINES - FOOTER_LINES;
const visibleItems = Math.max(5, contentHeight);
```

### 5. React.memo with Custom Comparator

```typescript
function arePropsEqual<T>(prev: Props<T>, next: Props<T>): boolean {
  if (prev.items.length !== next.items.length) return false;
  for (let i = 0; i < prev.items.length; i++) {
    if (prev.items[i].value !== next.items[i].value) return false;
  }
  return prev.selectedIndex === next.selectedIndex;
}

export const Select = React.memo(SelectComponent, arePropsEqual);
```

### 6. Multi-Screen Navigation

```typescript
type ScreenType = "main" | "detail" | "settings";

const [screenStack, setScreenStack] = useState<ScreenType[]>(["main"]);
const currentScreen = screenStack[screenStack.length - 1];

const navigateTo = (screen: ScreenType) => {
  setScreenStack(prev => [...prev, screen]);
};

const goBack = () => {
  if (screenStack.length > 1) {
    setScreenStack(prev => prev.slice(0, -1));
  }
};
```

## Detailed References

### Core Patterns
- [Component Patterns](references/component-patterns.md) - Screen/Part/Common architecture
- [Hooks Guide](references/hooks-guide.md) - Custom hook design patterns

### Advanced Topics
- [Multi-Screen Navigation](references/multi-screen-navigation.md) - Screen stack management
- [Animation Patterns](references/animation-patterns.md) - Spinners and progress bars
- [State Management](references/state-management.md) - Complex state patterns
- [Responsive Layout](references/responsive-layout.md) - Terminal size handling
- [Performance Optimization](references/performance-optimization.md) - Optimization techniques
- [Input Handling](references/input-handling.md) - Keyboard input patterns

### Troubleshooting
- [Ink Gotchas](references/ink-gotchas.md) - Common issues and solutions
- [Testing Patterns](references/testing-patterns.md) - ink-testing-library usage

## Examples

See [examples/](examples/) for practical implementation examples.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/akiojin) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
