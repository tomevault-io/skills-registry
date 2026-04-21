---
name: tauri-workflow-expert
description: Deep expertise in Tauri-specific development patterns, configuration, and security. Use when this capability is needed.
metadata:
  author: kuwa72
---
# Skill: Tauri Workflow Expert

This skill provides specialized knowledge for developing cross-platform applications using the Tauri framework.

## 🔑 Key Areas
- **Tauri Config**: Managing `tauri.conf.json`, identifying required permissions (`capabilities`).
- **Rust/JS Bridge**: Implementing `#[tauri::command]` and calling them via `@tauri-apps/api/core`.
- **System Integration**: Safely handling FS and OS operations via the Rust backend.
- **Build & Distribution**: Configuring build settings for Windows and other targets.

## 🛡️ Best Practices
- Avoid exposing sensitive system APIs directly to the frontend.
- Use strongly typed commands with TypeScript definitions.
- Keep the Rust backend lean; delegate UI logic to React.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kuwa72) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
