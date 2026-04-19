---
name: tauri-setup
description: > Use when this capability is needed.
metadata:
  author: stefkors
---

# Tauri v2 + React Setup Guide

Comprehensive guide documenting Tauri v2 + React setup patterns, configurations, and best practices learned from production work. This skill covers the full stack from Rust backend to React frontend, including build tooling, linting/formatting, and project organization.

## When to Apply

Reference this guide when:

- Setting up a new Tauri v2 + React project
- Implementing async/parallel processing in the Rust backend
- Adding native menus to Tauri applications
- Structuring React components with Base-UI
- Configuring linting and formatting tools
- Organizing project folder structure
- Integrating Tauri commands with React frontend
- Configuring Vite or Tauri build settings

## Topics Covered

### Frontend

- React 19 + TypeScript setup and component structure
- Base UI integration and usage patterns
- Async patterns and parallel API calls with `Promise.all`
- Vite configuration for Tauri development

### Backend

- Rust/Tauri command handlers and async patterns
- Native menus and menu event handling
- Tauri configuration and capabilities setup

### Tooling

- Linting with oxlint
- Formatting with oxfmt
- Feature-based folder structure

## Skill Structure

This skill is organized into focused rule files in the `rules/` directory:

- **rust-async-parallel.md** - Async runtime patterns, spawn_blocking usage, parallel processing with rayon
- **rust-menus.md** - Native menu implementation, context menus, menu event handling
- **react-setup.md** - React 19 setup, context patterns, state management, Tauri integration
- **base-ui.md** - Base-UI component usage, styling patterns, CSS modules
- **folder-structure.md** - Project organization, feature-based structure, component patterns
- **linting-formatting.md** - oxlint, TypeScript, oxfmt configuration

These rules are automatically applied when working with relevant files:

- Rust files (`src-tauri/**/*.rs`) → rust-async-parallel.md, rust-menus.md
- React files (`src/**/*.{ts,tsx}`) → react-setup.md, base-ui.md
- Config files (`*.json`, `*.ts`) → linting-formatting.md
- Project structure → folder-structure.md

## File-Specific Guidance

### React Files (`src/**/*.tsx`, `src/**/*.ts`)

- See `react-setup.md` for component patterns and hooks usage
- See `base-ui.md` for Base UI component integration
- See `folder-structure.md` for feature module organization

### Rust Files (`src-tauri/**/*.rs`)

- See `rust-async-parallel.md` for command and async patterns
- See `rust-menus.md` for native menu usage

### Configuration Files

- `tsconfig.json` → See `react-setup.md`
- `.oxlintrc.json` / `.oxfmtrc.json` → See `linting-formatting.md`

## Quick Reference

### Key Dependencies

**Rust (Cargo.toml):**

- `tauri = { version = "2", features = ["macos-private-api"] }`
- `tokio = { version = "1", features = ["rt-multi-thread", "sync", "time"] }`
- `rayon = "1"` (for parallel processing)
- `rusqlite = { version = "0.32", features = ["bundled", "backup"] }`
- `git2 = { version = "0.19", default-features = false, features = ["vendored-libgit2", "https"] }`

**Frontend (package.json):**

- `react = "^19.1.0"`
- `@base-ui-components/react = "^1.0.0-rc.0"`
- `@base-ui/react = "^1.1.0"`
- `@tauri-apps/api = "^2"`
- `vite = "^7.0.4"`
- `typescript = "~5.8.3"`
- `oxfmt = "^0.26.0"` (formatter)

### Core Patterns

1. **Async Commands**: Use `tauri::async_runtime::spawn_blocking` for blocking operations
2. **Parallel Processing**: Use `rayon` for CPU-intensive parallel work
3. **Menus**: Use Tauri's native menu API, not muda directly
4. **React Context**: Centralized app state with context provider pattern
5. **Base-UI**: Wrap Base-UI primitives with custom styled components
6. **CSS Modules**: Scoped styling with camelCase locals convention

## Related Skills

- `tauri-native-menus` - Detailed native menu implementation guide
- `rust-skills` - Comprehensive Rust coding guidelines
- `vercel-react-best-practices` - React performance optimization

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/stefkors) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
