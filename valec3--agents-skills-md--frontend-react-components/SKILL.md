---
name: frontend-react-components
description: Standardized guidelines and patterns for Frontend React Components. Use when this capability is needed.
metadata:
  author: valec3
---

# Frontend React Components

## When to use this skill
- Creating new React components
- Refactoring existing components
- Establishing component standards in a project
- Code reviews for component quality

## Workflow
- [ ] Identify component responsibility (single purpose)
- [ ] Choose component type (presentation vs container)
- [ ] Define prop interface with TypeScript
- [ ] Implement component with proper patterns
- [ ] Add prop validation and defaults
- [ ] Test component in isolation

## Instructions

### Component Structure

**Basic Functional Component**
```typescript
import { FC } from 'react';

interface ButtonProps {
  children: React.ReactNode;
  variant?: 'primary' | 'secondary';
  onClick?: () => void;
  disabled?: boolean;
}

export const Button: FC<ButtonProps> = ({
  children,
  variant = 'primary',
  onClick,
  disabled = false
}) => {
  return (
    <button
      className={`btn btn-${variant}`}
      onClick={onClick}
      disabled={disabled}
    >
      {children}
    </button>
  );
};
```

### Component Patterns

**1. Compound Components**
```typescript
export const Card = ({ children }: { children: React.ReactNode }) => {
  return <div className="card">{children}</div>;
};

Card.Header = ({ children }: { children: React.ReactNode }) => {
  return <div className="card-header">{children}</div>;
};

Card.Body = ({ children }: { children: React.ReactNode }) => {
  return <div className="card-body">{children}</div>;
};

// Usage
<Card>
  <Card.Header>Title</Card.Header>
  <Card.Body>Content</Card.Body>
</Card>
```

**2. Render Props**
```typescript
interface DataFetcherProps<T> {
  url: string;
  children: (data: T | null, loading: boolean) => React.ReactNode;
}

export function DataFetcher<T>({ url, children }: DataFetcherProps<T>) {
  const [data, setData] = useState<T | null>(null);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    fetch(url)
      .then(res => res.json())
      .then(data => {
        setData(data);
        setLoading(false);
      });
  }, [url]);

  return <>{children(data, loading)}</>;
}

// Usage
<DataFetcher<User> url="/api/user">
  {(user, loading) => (
    loading ? <Spinner /> : <UserProfile user={user} />
  )}
</DataFetcher>
```

**3. Controlled vs Uncontrolled**
```typescript
// Controlled
export function ControlledInput({ value, onChange }: {
  value: string;
  onChange: (value: string) => void;
}) {
  return (
    <input
      value={value}
      onChange={e => onChange(e.target.value)}
    />
  );
}

// Uncontrolled
export function UncontrolledInput({ defaultValue }: {
  defaultValue?: string;
}) {
  const inputRef = useRef<HTMLInputElement>(null);
  
  return <input ref={inputRef} defaultValue={defaultValue} />;
}
```

### Prop Design

**Do's:**
- Use TypeScript interfaces
- Provide sensible defaults
- Keep props simple and focused
- Use discriminated unions for variants

``` typescript
type ButtonProps = 
  | { variant: 'primary'; onClick: () => void }
  | { variant: 'link'; href: string };
```

**Don'ts:**
- Avoid boolean props for variants (use enums)
- Don't pass entire objects when you need one property
- Avoid deeply nested prop structures

### File Organization

```
components/
├── Button/
│   ├── Button.tsx
│   ├── Button.test.tsx
│   ├── Button.stories.tsx
│   └── index.ts
└── Card/
    ├── Card.tsx
    ├── CardHeader.tsx
    ├── CardBody.tsx
    ├── index.ts
    └── Card.test.tsx
```

## Resources
- Use composition over deep nesting
- Keep components focused (single responsibility)
- Prefer named exports for discoverability

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/valec3) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
