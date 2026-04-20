---
name: frontend-hooks-logic
description: specific patterns for React Hooks and Authentication in TMS Frontend. Use when this capability is needed.
metadata:
  author: smolagen
---

# Frontend Hooks Skill

## Custom Hooks Pattern
Place reusable logic in `frontend/src/hooks/`. Names must start with `use`.

### Structure
```typescript
import { useState, useEffect } from 'react';
import { apiClient } from '../api/client';

export function useMyFeature() {
  const [data, setData] = useState(null);
  
  // Logic here
  
  return { data, ... };
}
```

## Authentication (Telegram)
Use `useTelegramAuth` for handling auth state.
- **Dev Mode**: Automatically handled via `isDevMode()` check.
- **Token**: Stored in `localStorage` (`tms_auth_token`).
- **Interceptor**: `apiClient` automatically attaches the token.

## API Client
Always use the pre-configured `apiClient` from `../api/client`.
```typescript
import { apiClient } from '../api/client';

// Good
await apiClient.get('/users');

// Bad
fetch('/api/users');
```

## Context & Global State
- **Auth**: Handled by `useTelegramAuth` (local state + events).
- **Global Data**: Use Zustand stores (in `src/stores/`).

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/smolagen) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
