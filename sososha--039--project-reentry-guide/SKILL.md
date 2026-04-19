---
name: project-reentry-guide
description: > Use when this capability is needed.
metadata:
  author: sososha
---

# project-reentry-guide

## Canonical docs (Top 5)
1. semantic-cad-architecture.md — coordinates/units/grid/UX philosophy (primary)
2. architecture-overview.md — layers, APIs, HTTP schema
3. state-transitions.md — strict state machine + dirty resolution rules
4. floorplan-workspace-strategy.md — floorplan workspace design
5. wooden-frame-preset-plan.md — SRBA A-F + wooden preset plan

## Domain map (TOP 5 + alpha)
- kernel: src/kernel/ — exact geometry kernel (Point2/Line2/Circle2/Rect2), f64 correctness
- scene: src/scene/ — SceneContext/SceneWorld, ECS, dirty flags, GPU sync
- floorplan: src/floorplan/ — semantics layer (Space/Zone/Level), wall gen, resolution
- fsm: src/fsm/ — command FSM (Line/Circle/Rect/Move/Rotate/Scale/Space)
- renderer: src/renderer/ — wgpu pipeline, frame generation
- ui: src/ui/ — egui UI (menus, property panel)
- server: src/server/ — HTTP+JSON agent control (screenshot/state)

## Entry files (Top 5)
- src/main.rs — event loop, winit/egui integration, startup sequence
- src/context.rs — current SceneContext API implementation
- src/scene/context.rs — new SceneContext (design-doc aligned; future source of truth)
- src/floorplan/mod.rs — FloorplanModel (Space/Zone/Level)
- src/app.rs — App FSM, Msg/update, tool switching, global state

## Re-entry invariants (checklist)
- [ ] RH coords: X=East, Y=North, Z=Up, 1 unit = 1mm (semantic-cad-architecture.md §1)
- [ ] Scale is View; Model is always mm (semantic-cad-architecture.md §2)
- [ ] SceneContext is the only public API; do not touch ECS directly (architecture-overview.md, AGENTS.md)
- [ ] State rules: Display/Select/Highlight; Visible=false select/highlight is error (state-transitions.md)
- [ ] Dirty resolution order: Geometry -> Transform -> Visual (state-transitions.md)
- [ ] FSM split: System(Input)/App(Mode)/Command(Logic) (AGENTS.md)
- [ ] Dual rep: Kernel(f64) is truth; Mesh is disposable cache (architecture-overview.md)
- [ ] Design docs are authoritative; if code contradicts docs, update docs first (AGENTS.md, agent_checklist.md)
- [ ] SRBA layers A-F (wooden-frame-preset-plan.md)
- [ ] Try-lock policy for CursorMoved; blocking for important events (architecture-overview.md)

## Active TODOs (Top 10)
1. Space/Room input tool — src/fsm/space_rect.rs, src/fsm/space_poly.rs, floorplan-workspace-strategy.md
2. Floorplan palette UI — src/ui/, floorplan-workspace-strategy.md
3. SceneContext migration — src/context.rs -> src/scene/context.rs, SCENECONTEXT_MIGRATION.md
4. AxiomModel new (A layer) — src/axiom/ (planned), wooden-frame-preset-plan.md
5. WallComposition separation — src/composition/ (planned), wooden-frame-preset-plan.md
6. DispositionRules — src/disposition/ (planned), wooden-frame-preset-plan.md
7. Undo/Redo expansion — src/context.rs, addendum-03-persistence-and-undo.md
8. Drawing View — src/projection/ (WIP), projection_layer.md
9. Warning cleanup — whole project, AGENTS.md
10. Semantic Grid — src/grid.rs, semantic-cad-architecture.md

## Output file requirements: docs/design/START_HERE.md
Write/update these sections in order:
1) TL;DR (5 lines)
2) Current Focus / Last Good State / Next Action (3 short lines)
3) What to read first (Top docs)
4) Domain map
5) Entry files
6) Active TODOs
7) Invariants checklist
8) Start in 5 steps
9) Missing/unclear items

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sososha) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
