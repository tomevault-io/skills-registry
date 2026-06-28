---
name: extension-development
description: >- Use when this capability is needed.
metadata:
  author: podman-desktop
---

# Bootc Extension Development Guide

## Extension Lifecycle

### Entry Point (`packages/backend/src/extension.ts`)

`activate()` runs when Podman Desktop loads the extension. `deactivate()` cleans
up. Read the file to see the current initialization sequence:

```bash
cat packages/backend/src/extension.ts
```

## Architecture

Three packages in `packages/`:

- **Frontend** (`packages/frontend/`) — Svelte 5 webview UI. Root component: `src/App.svelte`. Reactive stores in `src/stores/`. Builds into `../backend/media/`.
- **Shared** (`packages/shared/`) — Typed API contract (`src/BootcAPI.ts`), data models (`src/models/`), and notification messages (`src/messages/Messages.ts`). Both frontend and backend depend on this package.
- **Backend** (`packages/backend/`) — Node.js extension host. Entry: `src/extension.ts`. Implements the shared API contract in `src/api-impl.ts` (`BootcApiImpl`). Uses `@podman-desktop/api` for Podman Desktop integration.

**Data flow**: Frontend calls `bootcClient.method()` → RPC `MessageProxy` serializes the call over webview `postMessage` → Backend `BootcApiImpl` handles it and returns the result back through the same channel. Backend pushes notifications to the frontend via `Messages` enum events.

For detailed UI patterns, see the **svelte-ui-design** skill.
For detailed backend API patterns, see the **backend-api** skill.

### RPC Communication

Frontend ↔ Backend uses a typed RPC proxy over webview `postMessage`:

- **Contract**: `packages/shared/src/BootcAPI.ts` — abstract class defining every method the frontend can call
- **Implementation**: `packages/backend/src/api-impl.ts` — `BootcApiImpl` extends the contract with real logic
- **Client**: `packages/frontend/src/api/client.ts` — hand-written file that creates a typed proxy at runtime via `rpcBrowser.getProxy<BootcApi>(BootcApi)`. Any method added to the abstract contract is automatically available on `bootcClient` without modifying this file.
- **Notifications**: `Messages` enum in `packages/shared/src/messages/Messages.ts` — backend-to-frontend push events

See the **backend-api** skill for detailed RPC patterns and examples.

## Adding a New Feature (End-to-End)

1. **Define API** — Add abstract method to `packages/shared/src/BootcAPI.ts`
2. **Implement** — Add implementation to `packages/backend/src/api-impl.ts` (see **backend-api** skill)
3. **Call from frontend** — Use `bootcClient.myNewMethod()` from `/@/api/client` (auto-proxied)
4. **Add route** — Add `<Route>` in `App.svelte`, navigation in `Navigation.svelte`
5. **Add models** — Define types in `packages/shared/src/models/` if needed
6. **Build UI** — See **svelte-ui-design** skill for layout and component patterns

## Build System

### Vite Configuration

**Backend** (`packages/backend/vite.config.js`):

- Library mode, CJS output → `dist/extension.js`
- Externals: `@podman-desktop/api`, `ssh2`, Node.js builtins
- Aliases: `/@/` → `src/`, `/@shared/` → `../../shared/`

**Frontend** (`packages/frontend/vite.config.js`):

- Svelte + Tailwind CSS plugins
- Output → `../backend/media/`
- Aliases: `/@/` → `src/`, `/@shared/` → `../../shared/`

### Development Watch

```bash
pnpm watch  # Watches all packages concurrently
```

Frontend changes rebuild into `packages/backend/media/`, which Podman Desktop
picks up when using `--extension-folder`.

## Extension Points (package.json)

### Extension Points

Configuration properties, menu contributions, commands, icons, and views are
all defined in `package.json` under `contributes`. Read it for the current state:

```bash
# See all extension points (configuration, menus, commands, views, icons)
cat packages/backend/package.json | grep -A 200 '"contributes"'
```

## Telemetry

Telemetry example:

```typescript
telemetryLogger.logUsage('buildDiskImage', { type: 'qcow2', arch: 'arm64' });
telemetryLogger.logError('buildFailed', { error: errorMessage });
```

---
> Source: [podman-desktop/extension-bootc](https://github.com/podman-desktop/extension-bootc) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-28 -->
