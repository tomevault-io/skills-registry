---
name: security-vite
description: |- Use when this capability is needed.
metadata:
  author: justinlevinedotme
---

<overview>

Security audit patterns for Vite applications focusing on environment variable exposure, build-time secrets, and SPA-specific vulnerabilities.

</overview>

<rules>

## Environment Variable Exposure

### The VITE_ Footgun

```
VITE_*    → Bundled into client JavaScript → Visible to everyone
No prefix → Only available in vite.config.ts → Safe for secrets
```

**Audit steps:**
1. `grep -r "VITE_" . -g "*.env*"`
2. Check `import.meta.env.VITE_*` usage in source
3. Common mistakes:
   - `VITE_API_SECRET` (SHOULD be server-only)
   - `VITE_DATABASE_URL` (MUST NOT use)
   - `VITE_STRIPE_SECRET_KEY` (only publishable keys)

### Env Files Priority

Vite loads in this order (later overrides earlier):
```
.env                # Always loaded
.env.local          # Always loaded, gitignored
.env.[mode]         # e.g., .env.production
.env.[mode].local   # e.g., .env.production.local, gitignored
```

**Check:** Are `.env.local` and `.env.*.local` in `.gitignore`?

### envPrefix Overrides

If `envPrefix` is configured, Vite exposes any variables with those prefixes. MUST treat `envPrefix` as a security-sensitive setting.

</rules>

<vulnerabilities>

## Build-Time vs Runtime

### Dangerous: Secrets in vite.config.ts

```typescript
// CRITICAL: Secret in config (ends up in bundle)
export default defineConfig({
  define: {
    'process.env.API_KEY': JSON.stringify(process.env.API_KEY),
  },
});

// The above makes API_KEY available in client code!
```

### Safe Pattern

```typescript
// Only use VITE_ prefix for truly public values
export default defineConfig({
  define: {
    '__APP_VERSION__': JSON.stringify(process.env.npm_package_version),
  },
});

// Keep secrets on server (use a backend API)
```

## Dev Server Security

### Open to Network

```typescript
// SHOULD NOT expose dev server to network without reason
export default defineConfig({
  server: {
    host: '0.0.0.0',  // or host: true
  },
});
```

This is dangerous on shared networks. MUST verify if intentional.

### Proxy Misconfiguration

```typescript
export default defineConfig({
  server: {
    proxy: {
      '/api': {
        target: 'http://localhost:3000',
        changeOrigin: true,
        // Missing secure options for production-like setup
      },
    },
  },
});
```

## SPA Security Issues

### Client-Side Auth Only

```typescript
// "Protection" only in React Router - NOT actual security
const ProtectedRoute = ({ children }) => {
  const { user } = useAuth();
  if (!user) return <Navigate to="/login" />;
  return children;
};

// API calls still need server-side auth!
// This is UI convenience, not security.
```

### Secrets in Bundle

```bash
# MUST check the built bundle for secrets
rg -a "(sk_live|sk_test|AKIA|api[_-]?key)" dist/
```

### Source Maps in Production

```typescript
// Check vite.config.ts
export default defineConfig({
  build: {
    sourcemap: true,  // SHOULD NOT expose source code in production
  },
});
```

</vulnerabilities>

<guidelines>

## Common Vulnerabilities

| Issue | Where to Look | Severity |
|-------|---------------|----------|
| VITE_* secrets | `.env*`, source files | CRITICAL |
| Secrets in define | `vite.config.ts` | CRITICAL |
| Source maps in prod | `vite.config.ts` | MEDIUM |
| Dev server exposed | `vite.config.ts` server.host | MEDIUM |
| Client-only auth | Route guards without API auth | HIGH |
| API keys in bundle | `dist/` directory | CRITICAL |

</guidelines>

<commands>

## Quick Audit Commands

```bash
# Find VITE_ secrets
grep -r "VITE_" . -g "*.env*"

# Find import.meta.env usage
rg 'import\.meta\.env' . -g "*.ts" -g "*.tsx" -g "*.vue"

# Check define in config
rg 'define:' vite.config.*

# Scan built bundle for secrets
rg -a "(sk_live|AKIA|ghp_|api[_-]?key['\"]?\s*[:=])" dist/

# Check for source maps
fd '\.map$' dist/
```

</commands>

<constraints>

## Hardening Checklist

- [ ] No secrets in `VITE_*` variables
- [ ] `.env.local` and `.env.*.local` in `.gitignore`
- [ ] `sourcemap: false` in production build
- [ ] `server.host` is not `0.0.0.0` or `true` (unless intentional)
- [ ] All sensitive API calls go through a backend (not direct from browser)
- [ ] No secrets in `vite.config.ts` define block

</constraints>

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/justinlevinedotme) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-12 -->
