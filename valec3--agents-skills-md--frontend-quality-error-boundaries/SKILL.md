---
name: frontend-quality-error-boundaries
description: Standardized guidelines and patterns for Frontend Quality Error Boundaries. Use when this capability is needed.
metadata:
  author: valec3
---

# Frontend Quality Error Boundaries

## When to use this skill
- Catching React errors
- Preventing app crashes
- Showing fallback UI

## Workflow
- [ ] Create error boundary component
- [ ] Wrap app/routes
- [ ] Log errors
- [ ] Show user-friendly fallback
- [ ] Add reset functionality

## Instructions

### Error Boundary
```typescript
class ErrorBoundary extends React.Component<
  { children: React.ReactNode },
  { hasError: boolean; error: Error | null }
> {
  state = { hasError: false, error: null };

  static getDerivedStateFromError(error: Error) {
    return { hasError: true, error };
  }

  componentDidCatch(error: Error, errorInfo: ErrorInfo) {
    console.error('Caught error:', error, errorInfo);
  }

  render() {
    if (this.state.hasError) {
      return (
        <div>
          <h1>Something went wrong</h1>
          <button onClick={() => this.setState({ hasError: false })}>
            Try again
          </button>
        </div>
      );
    }
    return this.props.children;
  }
}
```

### Usage
```typescript
<ErrorBoundary>
  <App />
</ErrorBoundary>
```

## Resources
- Wrap entire app or specific routes
- Log errors to monitoring service
- Provide reset mechanism

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/valec3) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
