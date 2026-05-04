---
name: arach
description: Personal meta-skill for Arach. Use this as the entry point to understand arach's projects, conventions, and available skills. Activates on "arach's projects", "what does arach work on", or when working in any arach/* repo. Use when this capability is needed.
metadata:
  author: neversight
---

# arach

Personal skill index for Arach (@arach). This is the entry point for understanding my projects, conventions, and how to work with my codebase.

## Conventions

**Always follow these when working on my projects:**

- Use **pnpm** over npm (check for pnpm-lock.yaml first)
- Add **gitmoji** to all commit messages (✨ feature, 🐛 fix, 🎨 improve, etc.)
- **NEVER** add co-authoring attribution or "Generated with Claude Code" footers
- Allow all Puppeteer uses without asking
- Prefer editing existing files over creating new ones

## Key Projects

### Documentation & Dev Tools

| Project | Description | Skill |
|---------|-------------|-------|
| **dewey** | Documentation toolkit for AI-agent-ready docs | `npx skills add arach/dewey` |
| **arc** | Visual architecture diagram editor | `npx skills add arach/arc` |
| **og** | Open Graph image generator | — |
| **hooked** | Voice & until loops for Claude Code | — |

### Apps — macOS/iOS

| Project | Description | Path |
|---------|-------------|------|
| **Talkie** | Voice conversation app | `~/dev/talkie` |
| **Scout** | Audio transcription | `~/dev/scout` |
| **Speakeasy** | Voice assistant | `~/dev/speakeasy` |
| **Pomo** | Pomodoro timer | `~/dev/pomo` |
| **Tempo** | Time tracking | `~/dev/tempo` |

### Web Properties

| Project | Description | Path |
|---------|-------------|------|
| **arach.dev** | Personal site | `~/dev/arach.dev` |
| **arach.io** | Portfolio | `~/dev/arach.io` |
| **usetalkie.com** | Talkie landing page | `~/dev/usetalkie.com` |
| **agentlist.io** | AI agent directory | `~/dev/agentlist.io` |

### Libraries & Experiments

| Project | Description | Path |
|---------|-------------|------|
| **agentloop** | Agent loop primitives | `~/dev/agentloop` |
| **fabric** | UI framework experiments | `~/dev/fabric` |

## Installing Skills

When working on a specific project, install its skill for deeper context:

```bash
# Install all arach skills (this meta-skill)
npx skills add arach/arach

# Install specific project skills
npx skills add arach/arc        # Architecture diagrams
npx skills add arach/dewey      # Documentation toolkit
```

## Project Detection

When I mention or you detect I'm working in:

| Context | Action |
|---------|--------|
| `~/dev/arc` or "architecture diagram" | Load arc-diagrams skill |
| `~/dev/dewey` or "documentation" | Load dewey-docs skill |
| `~/dev/talkie` or "voice app" | Swift/SwiftUI macOS app context |
| Any `~/dev/*` project | Check for local CLAUDE.md first |

## Tech Stack Preferences

| Category | Preference |
|----------|------------|
| Package manager | pnpm |
| Frontend | React + TypeScript + TailwindCSS |
| Desktop apps | Swift/SwiftUI (macOS), Tauri (cross-platform) |
| Build tools | Vite, Turbo |
| Testing | Vitest, Playwright |
| State | Zustand |

## Common Commands

```bash
# Development
pnpm dev          # Start dev server
pnpm build        # Production build
pnpm test         # Run tests
pnpm lint         # Lint code

# Swift/macOS
swift build       # Build Swift package
swift run         # Run in debug mode

# Git (always with gitmoji)
git commit -m "✨ Add new feature"
git commit -m "🐛 Fix bug in component"
git commit -m "🎨 Improve code structure"
git commit -m "📝 Update documentation"
git commit -m "🔧 Update configuration"
```

## Directory Structure

```
~/dev/
├── arach/          # This repo (GitHub profile + meta-skill)
├── arc/            # Architecture diagrams [has skill]
├── dewey/          # Documentation toolkit [has skill]
├── talkie/         # Voice conversation app
├── speakeasy/      # Voice assistant
├── arach.dev/      # Personal website
├── ...             # ~100 other projects
```

## When Starting Fresh

On a new machine, bootstrap everything:

```bash
# 1. Install this meta-skill
npx skills add arach/arach

# 2. Claude now knows all projects and can install specific skills as needed
```

## Links

- GitHub: [github.com/arach](https://github.com/arach)
- Site: [arach.dev](https://arach.dev)
- X: [@arach](https://x.com/arach)
- Site: [arach.io](https://arach.io)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neversight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
