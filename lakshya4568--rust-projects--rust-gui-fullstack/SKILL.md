---
name: rust-gui-fullstack
description: Senior Rust full-stack + GUI engineering guidance for building high-performance, memory-safe desktop apps and tooling. Use when implementing Rust GUIs (retained or immediate mode) with crates like Iced, Slint, egui, or Bevy; integrating low-level graphics and custom rendering with wgpu (and related Vulkan/OpenGL concepts); building async backends and app logic with Tokio + Axum/Actix; or when you need idiomatic Rust memory/concurrency patterns (lifetimes/borrowing, Arc, Mutex/RwLock, channels) for thread-safe UI + backend architectures. Use when this capability is needed.
metadata:
  author: lakshya4568
---

# Rust GUI Full-Stack

## Quick Start (Decision Tree)

1) Pick a UI approach
- Need native widgets + production desktop app quickly → prefer **Slint** (retained-mode, UI markup + Rust logic).
- Need lightweight immediate-mode tooling UI/debug panels → prefer **egui**.
- Need a Rust-native, fairly “app-like” GUI with widget tree → prefer **Iced**.
- Need a game/3D/visualization engine + ECS → prefer **Bevy**.

2) Pick a rendering approach
- Default: use the GUI’s renderer or wgpu integration.
- Need custom GPU pipelines, shaders, offscreen textures, or high-performance charts → use **wgpu** directly and embed the surface/texture into your GUI framework.

3) Pick a backend/runtime shape
- UI + async I/O, no HTTP server → single process with **Tokio**; UI thread + async tasks via channels.
- UI + local API server (plugin/automation) → **Axum** or **Actix Web** on Tokio; keep UI responsive with message passing.

If you’re unsure: start with **Tokio + channels**, keep UI state single-threaded, and treat async tasks as producers of messages/events.

## Workflow: Build a Responsive Rust Desktop App

1) Define state + messages (UI-driven architecture)
- Keep a small, well-typed `AppState` owned by the UI thread.
- Model external effects as commands/tasks that emit `Msg` back to the UI.

2) Isolate concurrency
- Prefer message passing over shared mutable state.
- If sharing is required: use `Arc<Mutex<T>>` / `Arc<RwLock<T>>` and keep lock scopes tiny.
- Never block the UI thread on I/O; do I/O in async tasks or worker threads.

3) Integrate rendering safely
- Treat GPU objects (device/queue/pipelines) as owned resources; avoid cloning heavy handles unless the API intends it.
- If the framework requires `'static` closures, move only lightweight handles (`Arc`, IDs) and keep actual state in `AppState`.

4) Structure the repo
- `crates/app` (GUI) owns state, routing, and view composition.
- `crates/core` owns domain types, validation, pure logic (no UI dependencies).
- `crates/io` owns I/O clients, persistence, network protocols (async).
- Optional `crates/render` owns wgpu pipelines/shaders.

## Patterns to Prioritize

### Zero-cost & idiomatic Rust
- Prefer iterators and borrowing over cloning.
- Prefer `&str` / `Cow<'a, str>` for text; avoid allocating in hot paths.
- Use `#[derive(Debug, Clone, Copy, Eq, PartialEq, Hash)]` where it helps ergonomics without hidden costs.

### Thread safety by design
- Use `Send + Sync` boundaries explicitly: background tasks produce `Msg` values; UI consumes.
- Prefer `tokio::sync::mpsc` / `oneshot` for command responses; avoid ad-hoc globals.

### UI performance
- Minimize per-frame allocations; cache layout and precompute expensive data.
- For immediate-mode GUIs: keep derived rendering data cached and invalidated on change.

## What to Load Next (References)

- Need to choose a GUI framework or understand their strengths/tradeoffs → read `references/gui-frameworks.md`.
- Need wgpu architecture, swapchain/surface rules, and shader/pipeline patterns → read `references/graphics-wgpu.md`.
- Need Tokio + Axum/Actix integration patterns and clean layering → read `references/backend-async.md`.
- Need Rust ownership/concurrency “recipes” for GUIs (lifetimes, `Arc`, `Mutex`, channels) → read `references/memory-concurrency.md`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/lakshya4568) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
