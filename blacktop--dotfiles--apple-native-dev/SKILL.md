---
name: apple-native-dev
description: | Use when this capability is needed.
metadata:
  author: blacktop
---

# Apple Native Development (CLI + Zed)

Build iOS/macOS apps without Xcode UI using XcodeGen, justfile, and Zed Editor.

## Core Workflow

### 1. Project Setup
```bash
# Install tools
brew install xcodegen just xcode-build-server

# Generate Xcode project from project.yml
cd Apps/MyApp && xcodegen generate
```

### 2. Credential Management
Use xcconfig files to keep Team ID out of git. See [references/credentials.md](references/credentials.md).

```
Apps/MyApp/Configs/
├── Project.xcconfig           # Committed (loads local + defaults)
├── Project.local.xcconfig     # Gitignored (your Team ID)
└── Project.local.xcconfig.example  # Committed template
```

**Quick setup** (auto-detects Team ID from keychain):
```bash
just ios::setup-credentials
```

### 3. Zed Editor LSP
Generate `buildServer.json` for code completion. See [references/zed-setup.md](references/zed-setup.md).

```bash
just ios::setup-lsp
# Or manually:
xcode-build-server config -project Apps/MyApp/MyApp.xcodeproj -scheme MyApp
```

### 4. Build & Deploy

**Simulator:**
```bash
just ios::build      # Build for simulator
just ios::run        # Build + install + launch
```

**Physical Device (iOS 17+):**
```bash
just ios::device-deploy  # Build + install + launch on iPhone
```

See [references/justfile.md](references/justfile.md) for all commands.

### 5. CI/CD
GitHub Actions scaffold at `.github/workflows/ios-build.yml.disabled`. See [references/ci-cd.md](references/ci-cd.md).

## Key Files

| File | Purpose |
|------|---------|
| `project.yml` | XcodeGen project definition |
| `Configs/Project.xcconfig` | Base build settings (committed) |
| `Configs/Project.local.xcconfig` | Credentials (gitignored) |
| `buildServer.json` | LSP configuration (gitignored) |
| `justfile` or `ios.just` | Build commands |

## Gitignore Patterns
```gitignore
# Credentials
*.local.xcconfig
!*.local.xcconfig.example

# Editor integration
buildServer.json

# XcodeGen output
*.xcodeproj/
```

## Templates

Copy from `assets/templates/` when bootstrapping:
- `Project.xcconfig` - Base xcconfig (committed)
- `Project.local.xcconfig.example` - Credential template (committed)
- `project.yml` - XcodeGen project definition
- `ios.just` - Justfile module for iOS commands
- `ios-build.yml.disabled` - GitHub Actions scaffold

## Troubleshooting

**"Unable to find module dependency" in CLI builds:**
Disable hardened runtime during development:
```yaml
# project.yml
settings:
  base:
    ENABLE_HARDENED_RUNTIME: NO
    ENABLE_ENHANCED_SECURITY: NO
```

**No code completion in Zed:**
Regenerate after building: `just ios::setup-lsp`

**Device install fails:**
Ensure device is trusted and connected: `just ios::devices`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/blacktop) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
