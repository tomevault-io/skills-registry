---
name: vendix-frontend-lazy-routing
description: Instructions for creating always lazy-loaded routes for sub-modules. Use when this capability is needed.
metadata:
  author: rzyfront
---
# Vendix Frontend Lazy Routing

> **ALWAYS LAZY** - Pattern for creating sub-module routes that load on demand to optimize bundle size and initial load time.

## 🎯 Core Principle

**NEVER** import feature components directly in routing files. 
**ALWAYS** use `loadChildren` for modules or `loadComponent` for standalone components.

## 📋 Route Configuration Patterns

### 1. Lazy Loading Standalone Components (Preferred)

Use this pattern when routing to a specific page or component that is standalone.

```typescript
// ✅ CORRECT: Lazy load component
{
  path: 'settings',
  loadComponent: () => 
    import('./settings/settings.component').then(m => m.SettingsComponent)
}

// ❌ INCORRECT: Eager load
// import { SettingsComponent } from './settings/settings.component';
// { path: 'settings', component: SettingsComponent }
```

### 2. Lazy Loading Child Modules/Routes

Use this pattern when a route has its own child routing (e.g., a feature module like 'shipping').

```typescript
// ✅ CORRECT: Lazy load child routes
{
  path: 'shipping',
  loadChildren: () => 
    import('./shipping/shipping.module').then(m => m.ShippingModule)
}
```

## 🛠️ Step-by-Step Implementation

### Step 1: Create the Component/Module
Ensure your target component is `standalone: true` or wrapped in a configured NgModule.

### Step 2: Define the Route
**CRITICAL**: Identify the actual parent routing file that is being used.
- For Store Admin features, this is often `apps/frontend/src/app/routes/private/store_admin.routes.ts`.
- Do NOT assume a local `settings.routes.ts` is active unless you verify it is imported.

1. **Do NOT import** the component at the top of the file.
2. Add the route object using `loadComponent` or `loadChildren`.

```typescript
// Example in store_admin.routes.ts
export const storeAdminRoutes: Routes = [
  {
    path: 'settings',
    children: [
      {
        path: 'shipping',
        loadChildren: () => import('@/modules/settings/shipping/shipping.module').then(m => m.ShippingModule)
      }
    ]
  }
];
```

### Step 3: Verify Chunk Generation
When building (or in `vendix_frontend` logs), verify that a separate chunk is created for your new route.

```
chunk-PG4JAFED.js | general-settings-component | 177.75 kB
```

## 🚨 Critical Rules

1. **No Static Imports**: Check the top of your `.routes.ts` file. If you see `import { FeatureComponent } ...`, delete it.
2. **Path Resolution**: Use relative paths (`./`) finding the component file.
3. **Typing**: Ensure the `then(m => m.ComponentName)` matches the exported class name exactly.

## 💡 Benefits
- **Performance**: Reduces the initial JavaScript bundle size.
- **Speed**: Faster time-to-interactive for the application.
- **Isolation**: Keeps features decoupled.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rzyfront) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
