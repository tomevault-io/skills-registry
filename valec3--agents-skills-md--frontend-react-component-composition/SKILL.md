---
name: frontend-react-component-composition
description: Standardized guidelines and patterns for Frontend React Component Composition. Use when this capability is needed.
metadata:
  author: valec3
---

# Frontend React Component Composition

## When to use this skill
- Building flexible, reusable component APIs
- Creating component libraries
- Avoiding prop drilling
- Implementing complex UI patterns

## Workflow
- [ ] Identify composition needs (children, slots, render props)
- [ ] Choose composition pattern
- [ ] Implement with proper TypeScript types
- [ ] Document usage examples
- [ ] Test composition scenarios

## Instructions

### Children Pattern

**Basic Children**
```typescript
interface ContainerProps {
  children: React.ReactNode;
}

export const Container = ({ children }: ContainerProps) => {
  return <div className="container">{children}</div>;
};
```

**Typed Children**
```typescript
import { ReactElement } from 'react';

interface TabsProps {
  children: ReactElement<TabProps> | ReactElement<TabProps>[];
}

export const Tabs = ({ children }: TabsProps) => {
  const tabs = React.Children.toArray(children);
  return <div>{tabs}</div>;
};
```

### Render Props Pattern

```typescript
interface MouseTrackerProps {
  render: (position: { x: number; y: number }) => React.ReactNode;
}

export function MouseTracker({ render }: MouseTrackerProps) {
  const [position, setPosition] = useState({ x: 0, y: 0 });

  useEffect(() => {
    const handleMove = (e: MouseEvent) => {
      setPosition({ x: e.clientX, y: e.clientY });
    };
    window.addEventListener('mousemove', handleMove);
    return () => window.removeEventListener('mousemove', handleMove);
  }, []);

  return <>{render(position)}</>;
}

// Usage
<MouseTracker
  render={({ x, y }) => (
    <div>Mouse at {x}, {y}</div>
  )}
/>
```

### Compound Components

```typescript
import { createContext, useContext, useState } from 'react';

// Context for internal state
interface TabsContextValue {
  activeTab: string;
  setActiveTab: (id: string) => void;
}

const TabsContext = createContext<TabsContextValue | null>(null);

export const Tabs = ({ children }: { children: React.ReactNode }) => {
  const [activeTab, setActiveTab] = useState('');

  return (
    <TabsContext.Provider value={{ activeTab, setActiveTab }}>
      <div className="tabs">{children}</div>
    </TabsContext.Provider>
  );
};

Tabs.List = ({ children }: { children: React.ReactNode }) => {
  return <div className="tab-list">{children}</div>;
};

Tabs.Tab = ({ id, children }: { id: string; children: React.ReactNode }) => {
  const context = useContext(TabsContext);
  if (!context) throw new Error('Tab must be used within Tabs');

  return (
    <button
      className={context.activeTab === id ? 'active' : ''}
      onClick={() => context.setActiveTab(id)}
    >
      {children}
    </button>
  );
};

Tabs.Panel = ({ id, children }: { id: string; children: React.ReactNode }) => {
  const context = useContext(TabsContext);
  if (!context) throw new Error('Panel must be used within Tabs');

  return context.activeTab === id ? <div>{children}</div> : null;
};

// Usage
<Tabs>
  <Tabs.List>
    <Tabs.Tab id="home">Home</Tabs.Tab>
    <Tabs.Tab id="profile">Profile</Tabs.Tab>
  </Tabs.List>
  <Tabs.Panel id="home">Home Content</Tabs.Panel>
  <Tabs.Panel id="profile">Profile Content</Tabs.Panel>
</Tabs>
```

### Slot Pattern

```typescript
interface LayoutProps {
  header?: React.ReactNode;
  sidebar?: React.ReactNode;
  content: React.ReactNode;
  footer?: React.ReactNode;
}

export const Layout = ({ header, sidebar, content, footer }: LayoutProps) => {
  return (
    <div className="layout">
      {header && <header>{header}</header>}
      <div className="main">
        {sidebar && <aside>{sidebar}</aside>}
        <main>{content}</main>
      </div>
      {footer && <footer>{footer}</footer>}
    </div>
  );
};

// Usage
<Layout
  header={<Header />}
  sidebar={<Nav />}
  content={<Article />}
  footer={<Footer />}
/>
```

### Higher-Order Components (HOC)

```typescript
function withLoading<P extends object>(
  Component: React.ComponentType<P>
) {
  return function WithLoadingComponent(
    props: P & { loading: boolean }
  ) {
    if (props.loading) {
      return <div>Loading...</div>;
    }
    return <Component {...props} />;
  };
}

// Usage
const UserProfileWithLoading = withLoading(UserProfile);
<UserProfileWithLoading user={user} loading={isLoading} />
```

### Forwarding Refs

```typescript
interface InputProps {
  label: string;
  error?: string;
}

export const Input = forwardRef<HTMLInputElement, InputProps>(
  ({ label, error, ...props }, ref) => {
    return (
      <div>
        <label>{label}</label>
        <input ref={ref} {...props} />
        {error && <span className="error">{error}</span>}
      </div>
    );
  }
);

Input.displayName = 'Input';
```

## Resources
- Compound components for flexible APIs
- Render props for behavior sharing
- Slots for layout composition
- Forward refs for form libraries

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/valec3) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
