---
name: apple-ecosystem-context
description: Assumes Apple ecosystem and provides macOS/iOS-specific guidance. Use when providing technical advice, terminal commands, or app recommendations. Use when this capability is needed.
metadata:
  author: scarabone
---

# Apple Ecosystem Context

## User's Apple Setup

**Computers:**
- MacBook Pro M4 Pro (24GB RAM) - primary machine
- MacBook Air M2 - secondary machine
- Both running latest macOS beta

**Mobile:**
- iPhone 16 Pro
- Apple Watch Series 8
- Running latest iOS/watchOS betas

**Key Characteristics:**
- Fully embedded in Apple ecosystem
- Beta tests macOS/iOS
- Apple Silicon (ARM architecture)
- Prefers native Apple solutions when available

## Default Assumptions

### Operating System
- Assume macOS unless stated otherwise
- Shell: zsh (macOS default since Catalina)
- Terminal: Recently added Warp (evaluate integration with workflow)
- Package manager: Homebrew when needed

### File Paths
Use macOS paths:
```
~/Library/Application Support/
~/Documents/
~/Desktop/
/usr/local/bin/          # Intel Homebrew
/opt/homebrew/bin/       # Apple Silicon Homebrew
```

NOT Linux paths like `/home/user/` or Windows paths

### Architecture Awareness
- Apple Silicon (ARM/M-series chips)
- Some software requires Rosetta 2
- Docker containers may need `--platform linux/arm64` or `linux/amd64`
- Note compatibility when recommending software

## Recommendation Priorities

### 1. Native Apple Solutions First
Examples:
- Shortcuts app for automation
- Notes app for note-taking
- Reminders for task management
- iCloud for sync/backup
- Apple Health for fitness tracking

### 2. Well-Integrated Third Party
Must meet:
- Native Apple Silicon support
- Deep macOS/iOS integration
- Respects Apple privacy standards
- Clean, Apple-like UI/UX

### 3. Cross-Platform When Necessary
Only when no good Apple-native option exists or when specific features require it.

## Common Patterns

### Package Installation
```bash
brew install package-name
```

For ARM-specific issues:
```bash
arch -arm64 brew install package-name
```

### Python/Node Setup
Assume Homebrew Python/Node:
```bash
brew install python@3.12
brew install node
```

NOT system Python at `/usr/bin/python`

### Docker on Apple Silicon
Note platform requirements when relevant:
```bash
docker run --platform linux/amd64 image-name
```

### Shortcuts Integration
When suggesting automation, consider:
- Can this be done with Apple Shortcuts?
- Does it need to integrate with iOS?
- Should it sync via iCloud?

## Integration with Existing Setup

User's homelab is NOT on macOS:
- Proxmox host: Linux (Debian-based)
- Home Assistant: Linux (supervised or container)
- Docker containers: Linux

So distinguish between:
- "On your Mac" (macOS commands)
- "On your Proxmox host" (Linux commands)
- "On Home Assistant" (HA-specific paths/commands)

## What NOT to Assume

❌ Don't assume Windows conventions (registry, PowerShell, etc.)
❌ Don't assume Linux-only tools without checking macOS alternatives
❌ Don't assume apt/yum/pacman package managers
❌ Don't assume systemd for services
❌ Don't assume Intel architecture

## Summary

Default to macOS-native solutions and conventions unless explicitly dealing with remote Linux systems (Proxmox, HA, Docker containers). When in doubt, mention the Apple-native approach first, then alternatives.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/scarabone) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
