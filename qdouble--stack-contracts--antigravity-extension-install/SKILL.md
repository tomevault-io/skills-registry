---
name: antigravity-extension-install
description: Install custom Antigravity/VS Code extensions into the editor and configure status bar buttons Use when this capability is needed.
metadata:
  author: qdouble
---

# Antigravity Extension Install

This skill covers installing custom extensions into **Antigravity** (the forked VS Code editor used across this workspace) and configuring per-workspace status bar buttons.

## Antigravity Editor Overview

Antigravity is a VS Code fork used across all machines in this ecosystem. It stores data in:

| Path | Purpose |
|------|---------|
| `/Applications/Antigravity.app` | Application bundle |
| `/Applications/Antigravity.app/Contents/Resources/app/bin/antigravity` | CLI binary |
| `~/.antigravity/extensions/` | Installed extensions directory |
| `~/.antigravity/extensions/extensions.json` | Extension manifest (auto-managed) |

> [!IMPORTANT]
> Always install into **Antigravity**, not VS Code. The CLI fallback chain is:
> 1. `/Applications/Antigravity.app/Contents/Resources/app/bin/antigravity`
> 2. `/Applications/Cursor.app/Contents/Resources/app/bin/code`
> 3. `/Applications/Visual Studio Code.app/Contents/Resources/app/bin/code`

## Custom Extensions

All custom extensions live in the **command-center** repo at:
```
command-center/extensions/
├── cross-agent-bridge/     # Delivers cross-agent messages to Gemini
│   ├── extension.js        # Main extension code
│   ├── package.json        # publisher: "command-center", name: "cross-agent-bridge"
│   ├── install.sh          # Self-contained installer script
│   └── *.vsix              # Pre-built packages (v1.0→2.0.3)
├── launch-button/          # Status bar launch buttons
│   ├── extension.js        # Reads .vscode/status-buttons.json
│   └── package.json        # publisher: "antigravity", name: "status-bar-buttons"
└── stack-prompt-bridge/    # Sends prompts to Gemini from external sources
    ├── extension.js
    └── package.json        # publisher: "stack-architect", name: "stack-prompt-bridge"
```

### Extension Identity Format

The installed folder name follows: `{publisher}.{name}-{version}`

| Extension | Publisher | Name | Installed As |
|-----------|-----------|------|-------------|
| Cross-Agent Bridge | `command-center` | `cross-agent-bridge` | `command-center.cross-agent-bridge-2.0.3` |
| Status Bar Buttons | `antigravity` | `status-bar-buttons` | `antigravity.status-bar-buttons-1.0.0` |
| Stack Prompt Bridge | `stack-architect` | `stack-prompt-bridge` | `stack-architect.stack-prompt-bridge-3.0.0` |

## How to Install an Extension

### Method 1: Via CLI (preferred)

If the extension has a `.vsix` package:

```bash
# Find the Antigravity CLI
ANTIGRAVITY_CLI="/Applications/Antigravity.app/Contents/Resources/app/bin/antigravity"

# Install from VSIX
"$ANTIGRAVITY_CLI" --install-extension path/to/extension.vsix --force

# Verify
"$ANTIGRAVITY_CLI" --list-extensions | grep extension-name
```

### Method 2: Direct Copy (for simple extensions without VSIX)

For extensions that are just `extension.js` + `package.json` (like `launch-button`):

// turbo
1. Read the extension's `package.json` to get `publisher` and `name` and `version`

// turbo
2. Copy the extension folder to `~/.antigravity/extensions/`:
```bash
# Format: {publisher}.{name}-{version}
cp -r path/to/extension ~/.antigravity/extensions/{publisher}.{name}-{version}
```

3. The `extensions.json` manifest will be auto-updated by Antigravity on next launch. No manual editing required.

4. Restart Antigravity: `Cmd+Shift+P` → "Developer: Reload Window"

### Method 3: Using install.sh (cross-agent-bridge only)

```bash
cd command-center/extensions/cross-agent-bridge
./install.sh
```

This packages a fresh `.vsix` and installs via CLI automatically.

## Installing on Remote Machines

To install an extension on a remote machine via SSH:

```bash
# Copy extension to remote
scp -r extensions/launch-button anthonywright@Anthonys-MacBook-Air.local:/tmp/

# Install via SSH
ssh anthonywright@Anthonys-MacBook-Air.local \
    'cp -r /tmp/launch-button ~/.antigravity/extensions/antigravity.status-bar-buttons-1.0.0'
```

## Status Bar Buttons

The `launch-button` extension reads `.vscode/status-buttons.json` from each workspace to create clickable status bar buttons.

### Config Format

Create `.vscode/status-buttons.json` in any workspace:

```json
{
    "buttons": [
        {
            "label": "Launch",
            "icon": "rocket",
            "command": "npm start",
            "tooltip": "Launch Application",
            "color": "#4ade80",
            "terminalName": "App Name"
        }
    ]
}
```

### Config Options

| Field | Required | Description |
|-------|----------|-------------|
| `label` | ✅ | Button text shown in status bar |
| `icon` | ❌ | VS Code Codicon name (e.g., `rocket`, `play`, `debug-start`) |
| `command` | ✅ | Shell command to run |
| `tooltip` | ❌ | Hover tooltip text |
| `color` | ❌ | Text color (hex, e.g., `#4ade80` for green) |
| `terminalName` | ❌ | Named terminal to reuse |
| `cwd` | ❌ | Working directory (relative to workspace root) |
| `alignment` | ❌ | `"left"` (default) or `"right"` |
| `priority` | ❌ | Numeric priority for ordering (higher = more left) |

### Existing Configs

| Workspace | Command | Tooltip |
|-----------|---------|---------|
| `command-center` | `npm start` | Launch Command Center |
| `stack-architect` | `npm run electron` | Launch Stack Architect |

### Creating for Other Repos

Electron apps should use their launch command:
```json
{ "command": "npm start", "tooltip": "Launch [App Name]" }
```

Web apps with dev servers:
```json
{ "command": "npm run dev", "tooltip": "Start [App Name] Dev Server", "icon": "play" }
```

## Extension Manifest (extensions.json)

> [!CAUTION]
> Do NOT manually edit `~/.antigravity/extensions/extensions.json`. Antigravity manages it automatically. Only use direct copy (Method 2) for extension files — the manifest updates on next launch.

### Manifest Entry Format (reference only)

```json
{
    "identifier": { "id": "publisher.name" },
    "version": "1.0.0",
    "location": {
        "$mid": 1,
        "path": "/Users/anthonywright/.antigravity/extensions/publisher.name-1.0.0",
        "scheme": "file"
    },
    "relativeLocation": "publisher.name-1.0.0",
    "metadata": {
        "installedTimestamp": 1770398312825,
        "source": "vsix"
    }
}
```

## Machine Ecosystem

| Machine | Hostname (.local) | Extensions Installed |
|---------|-------------------|---------------------|
| MacBook Pro | localhost | cross-agent-bridge, status-bar-buttons |
| MacBook Air | `Anthonys-MacBook-Air.local` | cross-agent-bridge, stack-prompt-bridge |
| Mac Mini | `Anthonys-Mac-mini.local` | (check remotely) |

### Network Config

Machine hostnames for SSH are stored in `~/.gemini/antigravity/network-config.json`:
```json
{
    "machines": [
        { "name": "macbook-pro", "host": "localhost", "type": "local" },
        { "name": "macbook-air", "host": "Anthonys-MacBook-Air.local", "user": "anthonywright", "type": "ssh" },
        { "name": "mac-mini", "host": "Anthonys-Mac-mini.local", "user": "anthonywright", "type": "ssh" }
    ]
}
```

> [!IMPORTANT]
> Always use `.local` (mDNS/Bonjour) hostnames, never hardcoded IPs. DHCP can reassign IPs when machines reconnect. The `.local` hostname resolves dynamically via Bonjour.

## Polyrepo Ecosystem

All repos live at `~/Developer/Antigravity Apps/`:

| Repository | Type | Has status-buttons? | Has custom extensions? |
|------------|------|---------------------|----------------------|
| command-center | Electron app | ✅ `npm start` | ✅ Source of all extensions |
| stack-architect | Electron app | ✅ `npm run electron` | ❌ |
| Stack-Analyzer-V2 | Web app | ❌ | ❌ |
| compound-research | Web app | ❌ | ❌ |
| navigator-hub | Web app | ❌ | ❌ |
| Vendor-Price-Tracker | Web app | ❌ | ❌ |
| stack-contracts | Shared types | ❌ | ❌ |
| stack-orchestrator | Orchestration | ❌ | ❌ |

## Legacy Extension (Deprecated)

The `vscode-extension/` folder at the repo root contains an older TypeScript-based extension (`command-center-bridge` v0.1.0). This was superseded by `cross-agent-bridge`. Do not use or install it.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/qdouble) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
