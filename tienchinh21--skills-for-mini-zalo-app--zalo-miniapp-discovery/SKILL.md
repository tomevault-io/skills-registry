---
name: zalo-miniapp-discovery
description: Explores and maps Zalo Mini App codebases. Use when asked to understand project structure, find features, audit zmp-sdk/zmp-ui usage, or analyze a ZMP codebase. Use when this capability is needed.
metadata:
  author: tienchinh21
---

# Zalo Mini App Discovery

Specialized discovery skill for Zalo Mini App (ZMP) projects built with React + TypeScript + Vite.

## When to Use

- "Project này có gì?", "Cấu trúc project thế nào?"
- "Feature X nằm ở đâu?", "Component Y hoạt động thế nào?"
- "Đang dùng zmp-sdk/zmp-ui ở đâu?", "Audit codebase"
- Exploring any Zalo Mini App project for the first time

## Discovery Pipeline

### Step 1: Detect Stack & Dependencies

Check `package.json` for ZMP dependencies:

```json
{
  "zmp-sdk": "^x.x.x",      // Zalo Mini App SDK
  "zmp-ui": "^1.x.x",       // ZaUI Components (v1.11.0 latest)
  "zmp-cli": "^x.x.x"       // CLI tools
}
```

Also check for common companions:
- State: `recoil`, `zustand`, `redux`
- Styling: `tailwindcss`, `sass`
- Router: `react-router-dom`

### Step 2: Identify Project Structure

Common ZMP project structures:

```
src/
├── pages/              # Page components (routes)
├── components/         # Shared components
├── hooks/              # Custom React hooks
├── utils/              # Helper functions
├── types/              # TypeScript interfaces
├── recoil/ or state/   # State management
├── assets/             # Static assets
└── app.tsx             # App entry point
```

Check for:
- `app-config.json` - ZMP configuration
- `zmp-cli.json` - CLI configuration
- Entry point structure (`App`, `Page`, `Header` from zmp-ui)

### Step 3: Map zmp-ui Usage

Search for ZaUI component imports:

```typescript
import { App, Page, Header, BottomNavigation } from 'zmp-ui';
import { Button, Input, Modal, Sheet } from 'zmp-ui';
import { List, Avatar, Swiper, Spinner } from 'zmp-ui';
```

**Layout Components** (critical - check these first):
- `App` - Root container (required)
- `Page` - Page wrapper (required per route)
- `Header` - Navigation header
- `BottomNavigation` - Tab bar

**Form Components**:
- Button, Input, Password, Search, TextArea, OTP
- Select, Picker, DatePicker
- Switch, Checkbox, Radio, Slider

**Display Components**:
- Avatar, List, Swiper, Progress, Spinner, Text
- Calendar, Icon, ImageViewer

**Overlay Components**:
- Modal, Sheet, ActionSheet, SnackbarProvider

### Step 4: Map zmp-sdk Usage

Search for SDK imports and usage patterns:

```typescript
import { ... } from 'zmp-sdk';
```

**Group by functionality:**

| Group | APIs to search |
|-------|---------------|
| User/Auth | `authorize`, `getUserID`, `getUserInfo`, `getAccessToken`, `getPhoneNumber`, `getSetting` |
| Events | `on`, `off`, `once`, `removeAllListeners`, `AppPaused`, `AppResumed`, `NetworkChanged` |
| Basic | `getAppInfo`, `getSystemInfo`, `getDeviceIdAsync`, `getContextAsync` |
| Routing | `closeApp`, `openMiniApp`, `openWebview`, `sendDataToPreviousMiniApp`, `getRouteParams` |
| Storage | `setItem`, `getItem`, `removeItem`, `clear`, `getStorageInfo` |
| UI | `showToast`, `closeLoading`, `configAppView`, `setNavigationBarColor`, `setNavigationBarTitle` |
| Location | `getLocation` |
| Media | `createCameraContext`, `chooseImage`, `openMediaPicker`, `downloadFile`, `openDocument` |
| Device | `scanQRCode`, `getNetworkType`, `vibrate`, `keepScreen`, biometric APIs |
| Zalo | `openProfile`, `openChat`, `followOA`, `openShareSheet`, `openPostFeed`, `createShortcut` |
| Permission | `requestSendNotification`, `openPermissionSetting` |

### Step 5: Identify Integration Patterns

Check for SDK wrapper patterns:

**Pattern A: Direct calls in components** (not recommended)
```typescript
// Inside component
const user = await getUserInfo();
```

**Pattern B: Custom hooks** (recommended)
```typescript
// hooks/useZmpUser.ts
export const useZmpUser = () => { ... }
```

**Pattern C: Service layer** (recommended for large apps)
```typescript
// services/zmp/user.ts
export const zmpUserService = { ... }
```

### Step 6: Check Event Listener Hygiene

Search for event subscriptions and verify cleanup:

```typescript
// Good: cleanup in useEffect
useEffect(() => {
  const handler = () => { ... };
  on('AppResumed', handler);
  return () => off('AppResumed', handler);
}, []);

// Bad: no cleanup, memory leak
on('AppResumed', () => { ... });
```

## Output Report Template

After discovery, provide structured report:

```markdown
## ZMP Project Analysis

### Stack
- zmp-sdk: v{version}
- zmp-ui: v{version}
- State: {recoil/zustand/none}
- Styling: {tailwind/scss/css}

### Structure
- Pages: {count} pages in {path}
- Components: {count} in {path}
- Hooks: {count} custom hooks

### zmp-ui Usage
- Layout: App ✓, Page ✓, Header ✓, BottomNavigation ✓/✗
- Forms: {list of used}
- Overlays: {list of used}

### zmp-sdk Usage
- User/Auth: {apis used}
- Events: {apis used}
- ...

### Integration Pattern
- {Pattern A/B/C}
- Event cleanup: {good/needs attention}

### Recommendations
- {actionable items}
```

## Anti-Patterns to Flag

1. **No App/Page wrapper** - Missing required ZaUI layout containers
2. **Direct SDK calls in UI** - Should use hooks/services
3. **Event listeners without cleanup** - Memory leaks
4. **Anonymous event handlers** - Can't be removed with `off()`
5. **Missing permission checks** - Before sensitive APIs (phone, camera)
6. **Hardcoded OA IDs** - Should be in config

## Official Documentation

When you need more details, refer to:
- **zmp-sdk API**: https://miniapp.zaloplatforms.com/documents/api/
- **zmp-ui Components**: https://miniapp.zaloplatforms.com/documents/zaui
- **Getting Started**: https://miniapp.zaloplatforms.com/documents/intro/getting-started/

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tienchinh21) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
