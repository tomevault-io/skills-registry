---
name: desktop-app
description: | Use when this capability is needed.
metadata:
  author: popup-studio-ai
---

# Desktop App Skill

> Build cross-platform desktop applications

## Frameworks

### Tauri (Recommended)

Lightweight, secure, Rust-based.

```bash
# Create new Tauri project
npm create tauri-app
cd my-app
npm run tauri dev
```

**Pros:**
- Small bundle size (~5MB)
- High performance
- Better security
- Use any frontend framework

### Electron

Chromium-based, widely used.

```bash
# Create with electron-forge
npm init electron-app@latest my-app
cd my-app
npm start
```

**Pros:**
- Mature ecosystem
- Large community
- Easy to learn
- Full Node.js access

## Project Structure (Tauri)

```
my-app/
├── src/                  # Frontend (React, Vue, etc.)
│   ├── App.tsx
│   └── main.tsx
├── src-tauri/            # Rust backend
│   ├── src/
│   │   └── main.rs
│   ├── tauri.conf.json
│   └── Cargo.toml
└── package.json
```

## Key Features

### System Tray

```rust
// src-tauri/src/main.rs
use tauri::SystemTray;

fn main() {
    let tray = SystemTray::new();

    tauri::Builder::default()
        .system_tray(tray)
        .run(tauri::generate_context!())
        .expect("error");
}
```

### Native Dialogs

```typescript
// Frontend
import { open, save } from '@tauri-apps/api/dialog';

async function openFile() {
  const selected = await open({
    filters: [{ name: 'Text', extensions: ['txt'] }]
  });
  return selected;
}
```

### File System

```typescript
import { readTextFile, writeTextFile } from '@tauri-apps/api/fs';

async function saveFile(path: string, content: string) {
  await writeTextFile(path, content);
}
```

## Deployment

- **macOS**: .dmg, .app
- **Windows**: .msi, .exe
- **Linux**: .deb, .AppImage

```bash
# Build for all platforms
npm run tauri build
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/popup-studio-ai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
