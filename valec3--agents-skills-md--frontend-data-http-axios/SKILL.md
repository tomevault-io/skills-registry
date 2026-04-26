---
name: frontend-data-http-axios
description: Standardized guidelines and patterns for Frontend Data Http Axios. Use when this capability is needed.
metadata:
  author: valec3
---

# Frontend Data Http Axios

## When to use this skill
- Making HTTP requests
- Configuring Axios
- Handling request/response

## Workflow
- [ ] Create axios instance
- [ ] Add interceptors
- [ ] Handle errors globally
- [ ] Type responses
- [ ] Cancel requests when needed

## Instructions

### Instance
```typescript
const api = axios.create({
  baseURL: '/api',
  timeout: 10000
});
```

### Request Interceptor
```typescript
api.interceptors.request.use(
  (config) => {
    const token = getToken();
    if (token) config.headers.Authorization = `Bearer ${token}`;
    return config;
  },
  (error) => Promise.reject(error)
);
```

### Response Interceptor
```typescript
api.interceptors.response.use(
  (response) => response,
  (error) => {
    if (error.response?.status === 401) {
      // Logout
    }
    return Promise.reject(error);
  }
);
```

## Resources
- Use instances for different APIs
- Add auth token in interceptors
- Handle common errors globally

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/valec3) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
