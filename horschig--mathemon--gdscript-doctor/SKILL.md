---
name: gdscript-doctor
description: Expert diagnosis and troubleshooting for GDScript logic errors and runtime crashes. Use when verification tasks fail or when the editor reports parsing/runtime warnings. Use when this capability is needed.
metadata:
  author: horschig
---

# GDScript Doctor

Systematic diagnosis and resolution of common Godot/GDScript errors.

## Workflow

1. **Reproduction**: Run the specific test file in GUT or launch the scene via F5.
2. **Trace Analysis**: Verify the line number and base object type in the Godot Output console.
3. **Lookup**: Match the symptom in [bug-library.md](references/bug-library.md).
4. **Fix & Verify**: 
   - Apply the correction (e.g., null check, deferred call).
   - Re-run benchmarks or unit tests 3x to ensure stability.

## Guidelines
- **Log Verbosity**: Use `push_error()` for fatal issues and `print()` for state tracking.
- **Node Discovery**: Use `mcp_godot-mcp_list_nodes` to find invalid paths.

## Artefacts to Update (bug fixes)

- When a fix requires changing APIs, data formats, or public contracts, add an entry to `./docs/todo/master_todo.md` describing the change and which files will be modified.
- Update `references/bug-library.md` with the discovered symptom and the fix pattern for future reference.
- Commit tests that reproduce the failure along with the fix and include the GUT summary in the PR description.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/horschig) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
