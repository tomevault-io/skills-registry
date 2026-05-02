---
name: web-ui-phase-closure
description: Close phase-13 web UI and XP editor gaps with MCP-first, user-flow Use when this capability is needed.
metadata:
  author: rikiyanai
---

# Web UI Phase Closure

Use this workflow for phase-13 closure work after B02.

## Scope Map

- `B03`: export controls parity in active result UX
- `B04`: dedicated sprite viewer page + route
- `B05`: unified viewer controls (play/pause/FPS/loop/nav/zoom/bg)
- `B06`: PNG-vs-XP side-by-side compare mode
- `C01-C06`: XP editor zoom, animation order parity, switch/context-menu workflows

## Execution Rules

1. Reproduce target behavior first with focused drivers.
2. Change only the minimum modules for one item ID at a time.
3. Verify with browser/runtime evidence before claiming complete.
4. Avoid broad-suite runs until the focused driver passes.

## File Targets By Item

- `B03`: `scripts/asset_gen/web_ui/app.js`, `scripts/asset_gen/web_ui/index.html`
- `B04-B06`: `scripts/asset_gen/web_ui/sprite_viewer/*`, web routes/templates
- `C01-C06`: `scripts/asset_gen/web_ui/workbench.js`, `scripts/asset_gen/web_ui/cell_editor.js`, `scripts/asset_gen/web_ui/merge_panel.js`

## Verification Commands

Run only the subset mapped to the touched item first.

```bash
# B02/B03-like wizard and export surface
python3 -m pytest scripts/asset_gen/tests/e2e/test_wizard_layout_editor.py -m e2e -o "addopts=" --browser chromium -q

# B04-B06 viewer model + loader contract
python3 -m pytest scripts/asset_gen/tests/test_frame_sequence.py scripts/asset_gen/tests/test_viewer_loaders.py -q

# Workbench/XP-editor browser-critical flow (C01-C06 safety net)
python3 -m pytest -o "addopts=" -m e2e --browser chromium \
  scripts/asset_gen/tests/e2e/test_workbench_behavior.py \
  -k "upload_extract_produces_sprites or extract_assign_populates_grid_with_thumbnails or export_produces_xp_file" -q
```

## Session Output Format

Return status as:

1. `Implemented`: item IDs changed.
2. `Verified`: exact commands/drivers with pass/fail.
3. `Still Broken`: item IDs and concrete failure.
4. `Blocked`: infra blockers only.
5. `Next`: next 1-3 item IDs.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rikiyanai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
