---
name: frontend-react-hooks-custom
description: Standardized guidelines and patterns for Frontend React Hooks Custom. Use when this capability is needed.
metadata:
  author: valec3
---

# Frontend React Hooks Custom

## When to use this skill
- Extracting reusable logic from components
- Creating custom data-fetching hooks
- Building form management hooks
- Sharing stateful logic across components

## Workflow
- [ ] Identify repeated logic in components
- [ ] Extract to custom hook
- [ ] Name with `use` prefix
- [ ] Define clear input/output contract
- [ ] Test hook in isolation
- [ ] Document usage examples

## Instructions

### Naming Convention

All hooks MUST start with `use`:
```typescript
// ✅ Good
useUser
useLocalStorage
usePrevious

// ❌ Bad
getUser
localStorage
previous
```

### Basic Custom Hook

```typescript
export function useToggle(initialValue = false) {
  const [value, setValue] = useState(initialValue);

  const toggle = useCallback(() => {
    setValue(v => !v);
  }, []);

  const setTrue = useCallback(() => setValue(true), []);
  const setFalse = useCallback(() => setValue(false), []);

  return { value, toggle, setTrue, setFalse };
}

// Usage
function Component() {
  const modal = useToggle();
  
  return (
    <>
      <button onClick={modal.setTrue}>Open</button>
      {modal.value && <Modal onClose={modal.setFalse} />}
    </>
  );
}
```

### Data Fetching Hook

```typescript
interface UseQueryOptions<T> {
  enabled?: boolean;
  refetchOnMount?: boolean;
  onSuccess?: (data: T) => void;
  onError?: (error: Error) => void;
}

export function useQuery<T>(
  key: string,
  fetcher: () => Promise<T>,
  options: UseQueryOptions<T> = {}
) {
  const [data, setData] = useState<T | null>(null);
  const [error, setError] = useState<Error | null>(null);
  const [isLoading, setIsLoading] = useState(false);

  const execute = useCallback(async () => {
    setIsLoading(true);
    setError(null);
    
    try {
      const result = await fetcher();
      setData(result);
      options.onSuccess?.(result);
    } catch (err) {
      const error = err as Error;
      setError(error);
      options.onError?.(error);
    } finally {
      setIsLoading(false);
    }
  }, [fetcher, options]);

  useEffect(() => {
    if (options.enabled !== false) {
      execute();
    }
  }, [key, options.enabled, execute]);

  const refetch = useCallback(() => {
    return execute();
  }, [execute]);

  return { data, error, isLoading, refetch };
}

// Usage
function UserProfile({ userId }: { userId: string }) {
  const { data: user, isLoading, error, refetch } = useQuery(
    `user-${userId}`,
    () => fetchUser(userId),
    {
      onSuccess: (user) => console.log('User loaded:', user),
      onError: (err) => console.error('Failed:', err)
    }
  );

  if (isLoading) return <div>Loading...</div>;
  if (error) return <div>Error: {error.message}</div>;
  
  return <div>{user?.name}</div>;
}
```

### Form Hook

```typescript
export function useForm<T extends Record<string, any>>(initialValues: T) {
  const [values, setValues] = useState(initialValues);
  const [errors, setErrors] = useState<Partial<Record<keyof T, string>>>({});
  const [touched, setTouched] = useState<Partial<Record<keyof T, boolean>>>({});

  const handleChange = useCallback((name: keyof T, value: any) => {
    setValues(prev => ({ ...prev, [name]: value }));
  }, []);

  const handleBlur = useCallback((name: keyof T) => {
    setTouched(prev => ({ ...prev, [name]: true }));
  }, []);

  const setFieldError = useCallback((name: keyof T, error: string) => {
    setErrors(prev => ({ ...prev, [name]: error }));
  }, []);

  const reset = useCallback(() => {
    setValues(initialValues);
    setErrors({});
    setTouched({});
  }, [initialValues]);

  return {
    values,
    errors,
    touched,
    handleChange,
    handleBlur,
    setFieldError,
    reset
  };
}

// Usage
function LoginForm() {
  const form = useForm({ email: '', password: '' });

  const handleSubmit = (e: React.FormEvent) => {
    e.preventDefault();
    if (!form.values.email) {
      form.setFieldError('email', 'Email is required');
      return;
    }
    // Submit logic
  };

  return (
    <form onSubmit={handleSubmit}>
      <input
        value={form.values.email}
        onChange={e => form.handleChange('email', e.target.value)}
        onBlur={() => form.handleBlur('email')}
      />
      {form.errors.email && <span>{form.errors.email}</span>}
    </form>
  );
}
```

### Local Storage Hook

```typescript
export function useLocalStorage<T>(key: string, initialValue: T) {
  const [storedValue, setStoredValue] = useState<T>(() => {
    try {
      const item = window.localStorage.getItem(key);
      return item ? JSON.parse(item) : initialValue;
    } catch (error) {
      console.error(error);
      return initialValue;
    }
  });

  const setValue = useCallback((value: T | ((val: T) => T)) => {
    try {
      const valueToStore = value instanceof Function ? value(storedValue) : value;
      setStoredValue(valueToStore);
      window.localStorage.setItem(key, JSON.stringify(valueToStore));
    } catch (error) {
      console.error(error);
    }
  }, [key, storedValue]);

  return [storedValue, setValue] as const;
}

// Usage
function ThemeSwitcher() {
  const [theme, setTheme] = useLocalStorage('theme', 'light');

  return (
    <button onClick={() => setTheme(theme === 'light' ? 'dark' : 'light')}>
      Current: {theme}
    </button>
  );
}
```

### Debounce Hook

```typescript
export function useDebounce<T>(value: T, delay: number): T {
  const [debouncedValue, setDebouncedValue] = useState(value);

  useEffect(() => {
    const handler = setTimeout(() => {
      setDebouncedValue(value);
    }, delay);

    return () => {
      clearTimeout(handler);
    };
  }, [value, delay]);

  return debouncedValue;
}

// Usage
function SearchInput() {
  const [search, setSearch] = useState('');
  const debouncedSearch = useDebounce(search, 500);

  useEffect(() => {
    if (debouncedSearch) {
      // Fetch results
      console.log('Searching for:', debouncedSearch);
    }
  }, [debouncedSearch]);

  return <input value={search} onChange={e => setSearch(e.target.value)} />;
}
```

### Previous Value Hook

```typescript
export function usePrevious<T>(value: T): T | undefined {
  const ref = useRef<T>();

  useEffect(() => {
    ref.current = value;
  }, [value]);

  return ref.current;
}

// Usage
function Counter() {
  const [count, setCount] = useState(0);
  const prevCount = usePrevious(count);

  return (
    <div>
      <p>Current: {count}</p>
      <p>Previous: {prevCount}</p>
      <button onClick={() => setCount(count + 1)}>Increment</button>
    </div>
  );
}
```

## Resources
- Always prefix with `use`
- Return objects for multiple values (not arrays)
- Use TypeScript generics for reusability
- Test hooks with `@testing-library/react-hooks`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/valec3) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
