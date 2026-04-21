---
name: update-documentation
description: > Use when this capability is needed.
metadata:
  author: brendankowitz
---

# Update Documentation

Trigger documentation updates based on recent code changes.

**Usage**: When user says "update documentation" or "document [scope]"

## Instructions

1. **Assess Changes**:
   - Identify recent code changes (commits, modified files).
   - Determine impact on existing documentation (API references, feature guides, architecture docs).

2. **Update Documentation**:
   - Use the Documentation Agent for comprehensive updates.
   - Update `README.md` if high-level features changed.
   - Update specific feature docs in `docs/`.
   - Update architecture diagrams or descriptions if designs changed.

3. **Verify**:
   - Ensure the documentation site (if applicable) builds.
   - Verify links and references are valid.
   - Ensure consistent style and tone.

## Scopes

- `feature`: Focus on a specific feature folder.
- `api`: Focus on API reference updates.
- `global`: Review and update project-level docs (README, Architecture).

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/brendankowitz) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
