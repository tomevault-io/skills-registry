---
name: phase-execution
description: Use when starting, executing, or completing any ROADMAP.md phase or sub-phase. Enforces the repeating workflow for every phase — read spec, implement in batches, test with determinism proof, update ROADMAP checkboxes, git commit and push. Triggers on phrases like "start phase", "continue phase", "proceed", "begin 1.3", or any reference to executing a ROADMAP item.
metadata:
  author: icl-system
---

# Phase Execution

Rigid workflow for executing any ROADMAP.md phase. Follow exactly — skipped steps cause rework.

## Before Starting

1. Read the phase items from `ICL-Runtime/ROADMAP.md`
2. Read relevant sections of `ICL-Spec/spec/CORE-SPECIFICATION.md` and `ICL-Spec/grammar/icl.bnf`
3. Read existing code that the phase builds on (e.g., for Phase 2, read parser output types)
4. Present a brief plan to the user — what will be built, in how many batches

## Implementation Loop

For each batch:

1. **Announce** — Tell user what this batch covers
2. **Implement** — Write the code
3. **Build** — `source "$HOME/.cargo/env" && cargo build --workspace`
4. **Test** — `cargo test --workspace` (all existing + new tests must pass)
5. **Determinism** — If the phase involves output, run 100-iteration determinism test
6. **Report** — Show results, ask user to type "continue" for next batch

## After All Batches Complete

1. **Full test suite** — `source "$HOME/.cargo/env" && cargo build --workspace && cargo test --workspace`
2. **Conformance** — Test against `ICL-Spec/conformance/valid/` and `ICL-Spec/conformance/invalid/` if applicable
3. **Determinism proof** — 100-iteration test on the completed component
4. **Update ICL-Runtime/ROADMAP.md** — Check off all completed items with `[x]`
5. **Git commit & push** — ALWAYS `cd` into the specific repo directory first (e.g., `cd <ICL_WORKSPACE>/ICL-Runtime`), then:
   ```bash
   cd <ICL_WORKSPACE>/<repo>  # ICL-Runtime, ICL-Spec, or ICL-Docs
   git add -A && git commit -m "feat(scope): Phase X.Y — description"
   git push
   ```
   **NEVER run git commands from the ICL workspace root** — it is NOT a git repo. The 3 repos are subdirectories.
   If ICL-Runtime/ROADMAP.md was updated, commit it from inside `ICL-Runtime`.
6. **Report** — Confirm phase complete, state what's next

## Environment

```bash
source "$HOME/.cargo/env"  # ALWAYS before any cargo command
```

SSH remotes use host alias `github-assetexpand2`, not `github.com`.

## Rules

- **Never skip the determinism test** — it's the core promise of ICL
- **Never skip ROADMAP update** — it's the single source of truth for progress
- **Never skip git commit+push** — every sub-phase gets committed
- **Batch size** — user controls when to continue; don't auto-proceed
- **Build before test** — always `cargo build` before `cargo test`
- **Spec is authoritative** — if code contradicts spec, fix the code

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/icl-system) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
