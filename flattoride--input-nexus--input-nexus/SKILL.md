---
name: input-nexus
description: Data-driven command palette / completion engine for Rust. Use when integrating `input-nexus` into a renderer (ratatui TUI, GPUI desktop GUI, web, etc.), when adding/organizing command templates + placeholders + dynamic choices, when implementing palette UX (candidates, placeholder editing, history), or when preparing a crates.io release for input-nexus. Use when this capability is needed.
metadata:
  author: flattoride
---

# Input Nexus

## Overview

Integrate and extend `input-nexus` as the single source of truth for command palette state (`InputState`), while keeping rendering and command execution in the host application.

## Workflow Decision Tree

- Need a palette/completion UI in a specific framework: implement a renderer adapter (event mapping + draw contract). See `references/integration.md`.
- Need to add/merge commands/contexts/placeholders: extend the registry and feed dynamic choices. See `Command Registry`.
- Need to cut a new release: follow the maintainer checklist. See `references/release.md`.

## Integration (Renderer Adapter)

1. Add dependency:
   - `input-nexus = "0.2.1"`
   - For local dev: use `[patch.crates-io] input-nexus = { path = "../input-nexus" }`
1. Construct an `InputController`:
   - `InputController::new()` then `register_context` + `register_command`, or
   - `InputController::with_registry(command_set![ ... ])`
1. Map framework input events into `InputKey`, then call `handle_key`:
   - Handle `InputResult::Completed(cmd)` by executing the command in your app.
   - Use `InputResult::Updated(state)` to re-render from the new state.
1. Render purely from `controller.state()` (`InputState`):
   - `buffer` + `cursor` is the single line the user is editing.
   - `phase` decides which extra UI to show (candidate list / placeholder editor).

See `references/integration.md` for adapter patterns and concrete mappings.

## Command Registry

1. Prefer template-driven definitions:
   - `command_set![ "id" => "ctx" => "br add <addr> [comment]" => "desc", ... ]`
   - `register_from_template(id, ctx, template, desc)` for runtime injection (scripts/plugins).
1. Use `update_placeholder_choices(command_id, placeholder, choices)` to inject dynamic lists (modules, processes, recent symbols, etc.).
1. Group in the UI:
   - Primary: `CommandSpec.context_id` (source grouping like built-in/script/internal).
   - Optional: `CommandSpec.group` for sub-grouping within a context (e.g. scripts/aslr).
   - Use `InputController::contexts()` / `InputController::context(id)` to render context labels/descriptions.

## Renderer Contract (What To Draw)

Renderers should treat `InputState` as a read-only snapshot:

- Always draw:
  - `buffer` and a caret at `cursor`.
- If `phase == SelectingCommand`:
  - Draw `completion_candidates` and highlight `candidate_index`.
  - Use `selected_command` for preview/details.
- If `phase == EditingPlaceholder`:
  - Highlight the `active_placeholder` segment in the buffer.
  - Use `placeholder_bindings` for field labels/values (empty values render as `<name>`).
  - If `choice_candidates` is non-empty, show a list and highlight `choice_index`.

## Key Semantics (Non-obvious)

Use these rules to avoid “why didn’t that key work?” issues:

- `Tab`:
  - `Idle`: populate candidates from current `buffer.trim()` and enter `SelectingCommand` if there is at least one match.
  - `SelectingCommand`: cycle candidate selection.
  - `EditingPlaceholder`: apply choice selection (if any), otherwise move to next placeholder.
- `Enter`:
  - `SelectingCommand`: confirm the selected command; enter `EditingPlaceholder` if it has placeholders.
  - `EditingPlaceholder`: complete only when all required placeholders are filled; otherwise move to next placeholder.
- `Space`:
  - `SelectingCommand`: confirm the selected command (same as `Enter`).
  - `EditingPlaceholder`: jump to next placeholder when not on the last placeholder; insert space when on the last placeholder (free-form rest-of-line values like comment/title/expression).
- `Backspace` in `EditingPlaceholder`:
  - Deletes inside the active placeholder.
  - When the placeholder becomes empty, it stays in placeholder editing (so the UI can keep showing `<name>`).
  - When the cursor is at the start of the first placeholder, another `Backspace` aborts placeholder editing and returns to `Idle` (keeping the command template in the buffer).
- `Up/Down`:
  - `SelectingCommand`: cycle candidates.
  - `EditingPlaceholder`: cycle `choice_candidates` if present; otherwise move between placeholders.
  - `Idle`: history navigation.
- `CtrlW`:
  - Implement it only for the “whole line” editing mode (not placeholder mode), unless you add placeholder-specific word delete. (`InputState::step` edits `buffer` directly.)

## Release (Maintainer)

Follow `references/release.md` to bump version, validate packaging, publish to crates.io, and tag.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/flattoride) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
