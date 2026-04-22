---
name: conductor
description: Operate Conductor context-driven development. Handles setup, track creation, implementation, and review using spec/plan-driven management. Use when this capability is needed.
metadata:
  author: hoonzinope
---

# Mission
Execute Conductor protocols: setup context, create tracks, implement tasks, and review work via state-aware track management.

## Workflow
State-driven management via Conductor artifacts:
1. **Context**: Resolve files via `references/conductor-extension/GEMINI.md`.
2. **Guardrails**: Verify required artifacts before command execution.
3. **Protocols**: Execute logic from `references/conductor-extension/commands/conductor/`.
4. **Authority**: Keep `tracks.md`, `spec.md`, and `plan.md` in sync.
5. **Quality**: Follow TDD flow from `references/conductor-extension/templates/workflow.md`.
- See `references/workflow.md` for detailed internal workflow steps.

## Rules
- Preserve Conductor as a state machine; no improvised transitions.
- Use explicit user confirmations for destructive or high-impact actions.
- Defer to referenced command/template files on conflict.

## References
- `references/workflow.md` - Detailed internal workflow.
- `references/conductor-extension/` - Command protocols and resolution rules.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hoonzinope) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
