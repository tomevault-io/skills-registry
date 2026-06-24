---
name: tauri-app
description: Bootstrap Tauri desktop applications with Rust backend and web frontend. Use when creating cross-platform desktop apps, Electron alternatives, or when the user wants a native desktop application with web technologies. Use when this capability is needed.
metadata:
  author: kadajett
---

# Tauri App Bootstrapper

Creates cross-platform desktop applications using Tauri v2 with Rust backend and your choice of frontend framework.

## Prerequisites

### Required Tools

```bash
# Check Rust
rustc --version && cargo --version

# Check Node.js (for frontend)
node --version && npm --version
```

### Platform-Specific Dependencies

#### Linux (Debian/Ubuntu)

```bash
sudo apt update
sudo apt install libwebkit2gtk-4.1-dev \
    build-essential \
    curl \
    wget \
    file \
    libxdo-dev \
    libssl-dev \
    libayatana-appindicator3-dev \
    librsvg2-dev
```

#### macOS

```bash
xcode-select --install
```

#### Windows

- Install [Microsoft Visual Studio C++ Build Tools](https://visualstudio.microsoft.com/visual-cpp-build-tools/)
- Install [WebView2](https://developer.microsoft.com/en-us/microsoft-edge/webview2/)

## Create Project with Official CLI

### Using create-tauri-app (Recommended)

```bash
# Interactive setup
npm create tauri-app@latest

# Or with specific options
npm create tauri-app@latest PROJECT_NAME -- --template react-ts
```

### Available Templates

| Template | Description |
|----------|-------------|
| `vanilla` | Plain HTML/CSS/JS |
| `vanilla-ts` | TypeScript without framework |
| `react` | React with JavaScript |
| `react-ts` | React with TypeScript |
| `vue` | Vue 3 with JavaScript |
| `vue-ts` | Vue 3 with TypeScript |
| `svelte` | Svelte with JavaScript |
| `svelte-ts` | Svelte with TypeScript |
| `solid` | SolidJS with JavaScript |
| `solid-ts` | SolidJS with TypeScript |
| `next` | Next.js |
| `nuxt` | Nuxt 3 |
| `leptos` | Rust Leptos (full Rust stack) |
| `yew` | Rust Yew (full Rust stack) |

## Recommended Template: React + TypeScript

```bash
npm create tauri-app@latest my-app -- --template react-ts
cd my-app
npm install
```

## Project Structure

```
my-tauri-app/
├── src/                    # Frontend source
│   ├── App.tsx
│   ├── main.tsx
│   └── styles.css
├── src-tauri/              # Rust backend
│   ├── src/
│   │   ├── main.rs         # Entry point
│   │   └── lib.rs          # Commands and logic
│   ├── Cargo.toml
│   ├── tauri.conf.json     # Tauri configuration
│   ├── capabilities/       # Permission capabilities
│   └── icons/              # App icons
├── package.json
├── vite.config.ts
└── tsconfig.json
```

## Tauri Configuration (tauri.conf.json)

Key settings to configure:

```json
{
  "$schema": "https://schema.tauri.app/config/2",
  "productName": "My App",
  "version": "0.1.0",
  "identifier": "com.yourcompany.myapp",
  "build": {
    "frontendDist": "../dist"
  },
  "app": {
    "windows": [
      {
        "title": "My App",
        "width": 1200,
        "height": 800,
        "resizable": true,
        "fullscreen": false
      }
    ],
    "security": {
      "csp": null
    }
  },
  "bundle": {
    "active": true,
    "targets": "all",
    "icon": [
      "icons/32x32.png",
      "icons/128x128.png",
      "icons/128x128@2x.png",
      "icons/icon.icns",
      "icons/icon.ico"
    ]
  }
}
```

## Adding Rust Commands

### Define commands in src-tauri/src/lib.rs

```rust
use tauri::Manager;

#[tauri::command]
fn greet(name: &str) -> String {
    format!("Hello, {}! You've been greeted from Rust!", name)
}

#[tauri::command]
async fn perform_action(app: tauri::AppHandle) -> Result<String, String> {
    // Async command example
    Ok("Action completed".to_string())
}

#[cfg_attr(mobile, tauri::mobile_entry_point)]
pub fn run() {
    tauri::Builder::default()
        .plugin(tauri_plugin_shell::init())
        .invoke_handler(tauri::generate_handler![greet, perform_action])
        .run(tauri::generate_context!())
        .expect("error while running tauri application");
}
```

### Call from frontend

```typescript
import { invoke } from "@tauri-apps/api/core";

async function callRust() {
  const result = await invoke<string>("greet", { name: "World" });
  console.log(result);
}
```

## Development Commands

```bash
# Start development server
npm run tauri dev

# Build for production
npm run tauri build

# Generate icons from a source image
npm run tauri icon path/to/icon.png
```

## Essential Tauri Plugins

Add these to `src-tauri/Cargo.toml`:

```toml
[dependencies]
tauri = { version = "2", features = [] }
tauri-plugin-shell = "2"
tauri-plugin-dialog = "2"
tauri-plugin-fs = "2"
tauri-plugin-http = "2"
tauri-plugin-notification = "2"
tauri-plugin-os = "2"
tauri-plugin-process = "2"
tauri-plugin-clipboard-manager = "2"
serde = { version = "1", features = ["derive"] }
serde_json = "1"
```

## Capabilities (Permissions)

Create `src-tauri/capabilities/default.json`:

```json
{
  "$schema": "https://schema.tauri.app/config/2",
  "identifier": "default",
  "description": "Default capabilities for the app",
  "windows": ["main"],
  "permissions": [
    "core:default",
    "shell:allow-open",
    "dialog:allow-open",
    "dialog:allow-save",
    "fs:allow-read",
    "notification:default"
  ]
}
```

## GitHub Actions CI

Create `.github/workflows/ci.yml`:

```yaml
name: CI

on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

jobs:
  build:
    strategy:
      fail-fast: false
      matrix:
        platform: [macos-latest, ubuntu-22.04, windows-latest]

    runs-on: ${{ matrix.platform }}
    steps:
      - uses: actions/checkout@v4

      - name: Setup Node
        uses: actions/setup-node@v4
        with:
          node-version: 20

      - name: Install Rust stable
        uses: dtolnay/rust-toolchain@stable

      - name: Install dependencies (Ubuntu)
        if: matrix.platform == 'ubuntu-22.04'
        run: |
          sudo apt-get update
          sudo apt-get install -y libwebkit2gtk-4.1-dev libappindicator3-dev librsvg2-dev patchelf

      - name: Install frontend dependencies
        run: npm ci

      - name: Build
        run: npm run tauri build
```

## Post-Setup Checklist

1. [ ] Update `tauri.conf.json` with your app details
2. [ ] Generate app icons: `npm run tauri icon`
3. [ ] Configure capabilities for required permissions
4. [ ] Set up code signing for distribution
5. [ ] Configure auto-updater if needed
6. [ ] Add platform-specific build scripts

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kadajett) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
