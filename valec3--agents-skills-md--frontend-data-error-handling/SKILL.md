---
name: frontend-data-error-handling
description: Standardized guidelines and patterns for Frontend Data Error Handling. Use when this capability is needed.
metadata:
  author: valec3
---

# Frontend Data Error Handling

## When to use this skill
- Handling API errors
- Showing error messages
- Implementing retry logic
- Error boundaries

## Workflow
- [ ] Catch errors at API layer
- [ ] Show user-friendly messages
- [ ] Log errors for debugging
- [ ] Implement retry for transient errors
- [ ] Use error boundaries for crashes

## Instructions

### API Error Handling
```typescript
try {
  const data = await fetchUser(id);
  return data;
} catch (error) {
  if (error.response?.status === 404) {
    throw new NotFoundError('User not found');
  }
  if (error.response?.status >= 500) {
    throw new ServerError('Server error, please try again');
  }
  throw error;
}
```

### React Error Boundary
```typescript
class ErrorBoundary extends React.Component {
  state = { hasError: false, error: null };

  static getDerivedStateFromError(error) {
    return { hasError: true, error };
  }

  componentDidCatch(error, errorInfo) {
    console.error('Error:', error, errorInfo);
  }

  render() {
    if (this.state.hasError) {
      return <ErrorFallback error={this.state.error} />;
    }
    return this.props.children;
  }
}
```

## Resources
- Show friendly messages to users
- Log detailed errors for debugging
- Retry transient failures

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/valec3) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
