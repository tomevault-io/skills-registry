---
name: doc-update
description: Update vive docs to match code changes. Use when this capability is needed.
metadata:
  author: k4h4shi
---

# Documentation Update Guide (vive)

コード変更に合わせてドキュメントを更新する。

## Mapping: Code to Docs

コード変更の種類に応じて更新先を決定する。

| Code Change Type    | Affected Files (src/)              | Target Doc (docs/)       | Action                                                    |
| :------------------ | :--------------------------------- | :----------------------- | :-------------------------------------------------------- |
| **Concept/Vision**  | N/A                                | `concept.md`             | Update high-level vision or core philosophy.              |
| **Architecture**    | `main.rs`, `core/`, `orchestrator/`| `architecture.md`        | Update component design, data flow, or tech stack details.|
| **Requirements**    | New features, CLI args             | `requirements.md`        | Update functional requirements list.                      |
| **UI/TUI Layout**   | `ui/`, `tui/`                      | `architecture.md`        | Update TUI layer descriptions.                            |

## Consistency Checklist

- [ ] Does `architecture.md` accurately reflect the current Rust struct/module structure?
- [ ] Are all implemented features marked as completed in `requirements.md`?
- [ ] Do unit/integration tests match the requirements?

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/k4h4shi) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
