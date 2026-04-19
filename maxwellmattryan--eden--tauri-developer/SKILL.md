---
name: tauri-developer
description: Tauri SDK development for desktop and mobile apps. Use when building cross-platform applications with Tauri, implementing IPC, managing permissions, or deploying to desktop (Windows/macOS/Linux) and mobile (iOS/Android) platforms. Use when this capability is needed.
metadata:
  author: maxwellmattryan
---

# Tauri Developer

## The Security Contract

Tauri applications run untrusted web content alongside trusted Rust code. You MUST:
- **Never trust frontend input** - validate and sanitize all IPC data in Rust
- **Use least privilege** - enable only the permissions your app needs
- **Scope access** - restrict file system and shell access to specific paths/commands
- **Validate origins** - check window labels and origins in commands

## Cardinal Rules

1. **Capabilities over permissions** - use the capability system to grant minimal access
2. **Rust handles security** - never implement security logic in JavaScript
3. **Validate at the boundary** - all IPC commands must validate their inputs
4. **Scope everything** - file paths, shell commands, HTTP domains
5. **Privacy by default** - don't collect data you don't need

## Quick Reference

| Topic | Reference |
|-------|-----------|
| Comprehensive security practices | [security.md](security.md) |
| Data protection, secure storage | [privacy.md](privacy.md) |
| HTTPS, API security, data in transit | [network-security.md](network-security.md) |
| Commands, invoke patterns, type safety | [ipc-commands.md](ipc-commands.md) |
| Capability system, scoped access | [permissions-capabilities.md](permissions-capabilities.md) |
| Official and community plugins | [plugins.md](plugins.md) |
| Loading states, accessibility, UX | [ux-patterns.md](ux-patterns.md) |
| iOS/Android development | [mobile-development.md](mobile-development.md) |
| Windows/macOS/Linux development | [desktop-development.md](desktop-development.md) |
| Managed state, persistence | [state-management.md](state-management.md) |
| Building, signing, distribution | [build-distribution.md](build-distribution.md) |

## Project Structure

```
my-app/
├── src/                    # Frontend (Svelte v5)
│   ├── lib/
│   │   ├── components/
│   │   └── tauri.ts       # Typed IPC wrappers
│   ├── routes/
│   └── app.html
├── src-tauri/
│   ├── src/
│   │   ├── main.rs        # Entry point
│   │   ├── lib.rs         # Command definitions
│   │   └── commands/      # Command modules
│   ├── capabilities/      # Permission definitions
│   ├── tauri.conf.json    # App configuration
│   └── Cargo.toml
├── package.json
└── svelte.config.js
```

## Basic Command Pattern

```rust
// src-tauri/src/lib.rs
use tauri::command;

#[command]
fn greet(name: &str) -> Result<String, String> {
    // Always validate input
    if name.is_empty() {
        return Err("Name cannot be empty".into());
    }
    if name.len() > 100 {
        return Err("Name too long".into());
    }
    Ok(format!("Hello, {}!", name))
}

#[cfg_attr(mobile, tauri::mobile_entry_point)]
pub fn run() {
    tauri::Builder::default()
        .invoke_handler(tauri::generate_handler![greet])
        .run(tauri::generate_context!())
        .expect("error while running tauri application");
}
```

```typescript
// src/lib/tauri.ts
import { invoke } from '@tauri-apps/api/core';

export async function greet(name: string): Promise<string> {
    return invoke<string>('greet', { name });
}
```

```svelte
<!-- src/routes/+page.svelte -->
<script lang="ts">
    import { greet } from '$lib/tauri';

    let name = $state('');
    let message = $state('');
    let loading = $state(false);

    async function handleGreet() {
        loading = true;
        try {
            message = await greet(name);
        } catch (error) {
            message = `Error: ${error}`;
        } finally {
            loading = false;
        }
    }
</script>

<input bind:value={name} placeholder="Enter name" />
<button onclick={handleGreet} disabled={loading}>
    {loading ? 'Loading...' : 'Greet'}
</button>
<p>{message}</p>
```

## Configuration Essentials

```json
// tauri.conf.json
{
  "$schema": "https://schema.tauri.app/config/2",
  "productName": "My App",
  "version": "0.1.0",
  "identifier": "com.example.myapp",
  "build": {
    "frontendDist": "../build"
  },
  "app": {
    "withGlobalTauri": false,
    "security": {
      "csp": "default-src 'self'; script-src 'self'"
    }
  },
  "bundle": {
    "active": true,
    "targets": "all",
    "icon": ["icons/icon.png"]
  }
}
```

## Svelte v5 Integration Notes

- Use `$state()` for reactive state (replaces `let` reactivity)
- Use `$derived()` for computed values (replaces `$:`)
- Use `$effect()` for side effects (replaces reactive statements)
- Wrap Tauri API calls in typed functions in `$lib/tauri.ts`
- Handle loading and error states explicitly in components

## Platform Commands

```bash
# Development
npm run tauri dev              # Desktop dev server
npm run tauri android dev      # Android dev
npm run tauri ios dev          # iOS dev

# Building
npm run tauri build            # Desktop release
npm run tauri android build    # Android APK/AAB
npm run tauri ios build        # iOS IPA
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/maxwellmattryan) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
