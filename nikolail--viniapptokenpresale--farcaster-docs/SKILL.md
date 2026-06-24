---
name: farcaster-miniapp-sdk
description: Farcaster MiniApp SDK documentation for building mini apps. Use when developing Farcaster mini apps, working with the @farcaster/miniapp-sdk, handling context, actions, haptics, navigation, or wallet integrations in the Farcaster ecosystem. Use when this capability is needed.
metadata:
  author: nikolail
---

# Farcaster MiniApp SDK

Comprehensive documentation for building Mini Apps on Farcaster using the `@farcaster/miniapp-sdk`. Covers context, actions, haptics, navigation, capability detection, and client events.

## When to Apply

Reference this documentation when:
- Building a new Farcaster Mini App
- Accessing user/client/location context via `sdk.context`
- Implementing SDK actions (composeCast, viewProfile, openUrl, etc.)
- Adding haptic feedback to your mini app
- Handling back navigation
- Detecting platform capabilities and supported chains
- Listening to client events (miniappAdded, notificationsEnabled, etc.)
- Integrating wallet/token operations (swapToken, sendToken, viewToken)

## SDK Categories

| Category | Description | Key Methods |
|----------|-------------|-------------|
| Context | Session info about user, client, location | `sdk.context` |
| Actions | User-facing operations | `addMiniApp`, `composeCast`, `close`, `ready`, `openUrl`, `openMiniApp`, `viewProfile`, `viewCast`, `swapToken`, `sendToken`, `viewToken` |
| Haptics | Physical feedback | `impactOccurred`, `notificationOccurred`, `selectionChanged` |
| Back Navigation | In-app navigation support | `enableWebNavigation`, `show`, `hide` |
| Capabilities | Feature detection | `getCapabilities`, `getChains`, `isInMiniApp` |
| Events | Client-emitted events | `miniappAdded`, `miniappRemoved`, `notificationsEnabled`, `notificationsDisabled` |

## Quick Reference

### Context (`sdk.context`)

Access session information when your app opens:

- `user` - FID, username, displayName, pfpUrl, location
- `client` - platformType ('web'|'mobile'), clientFid, added, safeAreaInsets, notificationDetails
- `location` - Launch context: `cast_embed`, `cast_share`, `notification`, `launcher`, `channel`, `open_miniapp`
- `features` - haptics, cameraAndMicrophoneAccess

### Actions (`sdk.actions.*`)

| Action | Purpose |
|--------|---------|
| `ready()` | Hide splash screen |
| `close()` | Close the mini app |
| `addMiniApp()` | Prompt user to add app |
| `composeCast({ text, embeds, parent, channelKey })` | Open cast composer |
| `openUrl(url)` | Open external URL |
| `openMiniApp({ url })` | Navigate to another mini app |
| `viewProfile({ fid })` | Display user profile |
| `viewCast({ hash })` | Open a specific cast |
| `swapToken({ sellToken, buyToken, sellAmount })` | Open swap form |
| `sendToken({ token, amount, recipientFid })` | Open send form |
| `viewToken({ token })` | Display token info |
| `requestCameraAndMicrophoneAccess()` | Request media permissions |

### Haptics (`sdk.haptics.*`)

```ts
// Impact feedback: 'light' | 'medium' | 'heavy' | 'soft' | 'rigid'
await sdk.haptics.impactOccurred('medium')

// Notification feedback: 'success' | 'warning' | 'error'
await sdk.haptics.notificationOccurred('success')

// Selection feedback
await sdk.haptics.selectionChanged()
```

### Back Navigation (`sdk.back.*`)

```ts
// Auto-integrate with browser navigation
await sdk.back.enableWebNavigation()

// Manual control
sdk.back.onback = () => { /* custom handler */ }
await sdk.back.show()
await sdk.back.hide()
```

### Capability Detection

```ts
// Check supported features
const capabilities = await sdk.getCapabilities()
const supportsCompose = capabilities.includes('actions.composeCast')

// Check supported chains (CAIP-2 identifiers)
const chains = await sdk.getChains()

// Detect if running in mini app context
const isMiniApp = await sdk.isInMiniApp()
```

### Client Events

```ts
sdk.on('miniappAdded', () => { /* user added app */ })
sdk.on('miniappRemoved', () => { /* user removed app */ })
sdk.on('notificationsEnabled', () => { /* notifications on */ })
sdk.on('notificationsDisabled', () => { /* notifications off */ })

// Cleanup
sdk.removeListener('miniappAdded', handler)
sdk.removeAllListeners()
```

## Quick Auth (Authentication for API Routes)

Quick Auth provides authenticated sessions for Farcaster users. Use it to protect API endpoints that access user-specific data.

### When to Use Quick Auth

| Data Type | Needs Auth? | Example |
|-----------|-------------|---------|
| User's own data (CRUD) | **Yes** | My todos, my settings, my scores |
| Private user content | **Yes** | Direct messages, private notes |
| User-specific actions | **Yes** | Delete my post, update my profile |
| Public leaderboards (read) | No | Top 100 scores |
| Public content (read) | No | Browse all posts |
| Static app data | No | Game rules, help text |

**Rule**: If the user should only access THEIR data, use Quick Auth.

### Frontend: Making Authenticated Requests

Use `sdk.quickAuth.fetch` for authenticated API calls:

```tsx
import { sdk } from "@farcaster/miniapp-sdk";

// Authenticated fetch - automatically adds Bearer token
const response = await sdk.quickAuth.fetch("/api/todos");
const todos = await response.json();

// For mutations
await sdk.quickAuth.fetch("/api/todos", {
  method: "POST",
  headers: { "Content-Type": "application/json" },
  body: JSON.stringify({ title: "New todo" }),
});
```

Or get the token directly:

```tsx
const { token } = await sdk.quickAuth.getToken();
// Use token in custom fetch calls
```

### Backend: Validating Tokens

Install the Quick Auth library:

```bash
yarn add @farcaster/quick-auth
```

Create an auth utility `packages/nextjs/utils/auth.ts`:

```typescript
import { createClient, Errors } from '@farcaster/quick-auth';

const client = createClient();

export async function getAuthenticatedFid(request: Request): Promise<number | null> {
  const authorization = request.headers.get('Authorization');
  if (!authorization?.startsWith('Bearer ')) {
    return null;
  }

  try {
    const token = authorization.split(' ')[1];
    const payload = await client.verifyJwt({
      token,
      domain: process.env.NEXT_PUBLIC_URL || 'localhost:3000',
    });
    return payload.sub; // FID of authenticated user
  } catch (e) {
    if (e instanceof Errors.InvalidTokenError) {
      console.info('Invalid token:', e.message);
    }
    return null;
  }
}

export async function requireAuth(request: Request): Promise<number> {
  const fid = await getAuthenticatedFid(request);
  if (!fid) {
    throw new Error('Unauthorized');
  }
  return fid;
}
```

### Protected API Route Example

```typescript
import { sql } from '~~/utils/db';
import { requireAuth } from '~~/utils/auth';
import { NextResponse } from 'next/server';

// GET /api/todos - Get MY todos only
export async function GET(request: Request) {
  try {
    const fid = await requireAuth(request);
    
    // Only return todos owned by this user
    const todos = await sql`
      SELECT * FROM todos 
      WHERE owner_fid = ${fid}
      ORDER BY created_at DESC
    `;
    
    return NextResponse.json(todos);
  } catch {
    return NextResponse.json({ error: 'Unauthorized' }, { status: 401 });
  }
}

// DELETE /api/todos/[id] - Delete MY todo only
export async function DELETE(request: Request) {
  try {
    const fid = await requireAuth(request);
    const { id } = await request.json();
    
    // Only delete if user owns this todo
    const result = await sql`
      DELETE FROM todos 
      WHERE id = ${id} AND owner_fid = ${fid}
      RETURNING id
    `;
    
    if (result.length === 0) {
      return NextResponse.json({ error: 'Not found or not authorized' }, { status: 404 });
    }
    
    return NextResponse.json({ success: true });
  } catch {
    return NextResponse.json({ error: 'Unauthorized' }, { status: 401 });
  }
}
```

### Performance Optimization

Add preconnect hint in your app:

```tsx
import { preconnect } from 'react-dom';

function AppRoot() {
  preconnect("https://auth.farcaster.xyz");
}
```

Or in HTML:
```html
<link rel="preconnect" href="https://auth.farcaster.xyz" />
```

---

## Important Patterns

### Safe Area Insets

Handle mobile navigation elements:

```tsx
<div style={{
  marginTop: context.client.safeAreaInsets?.top,
  marginBottom: context.client.safeAreaInsets?.bottom,
  marginLeft: context.client.safeAreaInsets?.left,
  marginRight: context.client.safeAreaInsets?.right,
}}>
  {/* app content */}
</div>
```

### Location Context Types

- `cast_embed` - Launched from a cast where app is embedded
- `cast_share` - User shared a cast to your app
- `notification` - Launched from app notification
- `launcher` - Launched from client app directly
- `channel` - Launched from a channel context
- `open_miniapp` - Launched from another mini app (includes `referrerDomain`)

### Token Identifiers (CAIP-19)

```ts
// Format: eip155:{chainId}/erc20:{contractAddress}
const baseUsdc = "eip155:8453/erc20:0x833589fCD6eDb6E08f4c7C32D4f71b54bdA02913"

// Native token
const opEth = "eip155:10/native"
```

## How to Use

Read individual rule files for detailed explanations and code examples:

```
rules/context.md
rules/actions-compose-cast.md
rules/haptics.md
rules/back-navigation.md
rules/capabilities.md
rules/client-events.md
```

### All Rule Files

| Category | Files |
|----------|-------|
| Context | `context.md` |
| Actions | `actions-add-miniapp.md`, `actions-compose-cast.md`, `actions-close.md`, `actions-ready.md`, `actions-open-url.md`, `actions-open-miniapp.md`, `actions-view-profile.md`, `actions-view-cast.md`, `actions-swap-token.md`, `actions-send-token.md`, `actions-view-token.md`, `actions-request-camera-microphone.md` |
| Haptics | `haptics.md` |
| Navigation | `back-navigation.md` |
| Capabilities | `capabilities.md`, `is-in-miniapp.md` |
| Events | `client-events.md` |

Each rule file contains:
- Brief explanation of the feature
- TypeScript usage examples
- Parameters and return types
- Best practices and notes

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/nikolail) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
